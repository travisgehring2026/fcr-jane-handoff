# Lessons Learned + Anti-Patterns

**Last updated:** April 15, 2026
Rules derived from real failures. Every item has a story behind it.

## Bridge failures (April 14 Laura crisis)

- **CMD: extraction grabbed only first line of multi-line heredocs.** Bridge v1 silently dropped the rest. Fix: v2 requires fenced bash blocks.
- **Windows line endings (\r\n) broke Python parser.** Fix: v2 normalizes \r\n → \n on incoming task body.
- **Zero alerting on silent failure.** Jane acknowledged 10 tasks, executed 0, nobody noticed for 14 hours. Fix: WhatsApp alerts on every failure + SSL exception.
- **Bridge never read [JANE-RESULT] back.** Fix: result drafts now start with explicit STATUS/EXIT_CODE header.
- **If Jane fails 3 times, she should email Travis and WAIT.** This rule existed but was never enforced during Laura crisis.

## Env file gotchas

- **`source /root/fcr.env` requires `set -a` flag.** Without it, variables aren't exported to subshells. Always use: `set -a; source /root/fcr.env; set +a`
- **WordPress app passwords have spaces.** Values like `cMgM stXI 85Y8 562R FPaM EDOk` must be single-quoted in the env file or bash interprets each word as a command.
- **UNSPLASH_ACCESS_KEY also had spaces.** Same fix.
- **Env file had the wrong path in self_healer.py.** Referenced `/root/shop-ai/.env` (old server) and even had a typo `/root/shop-a/.env`. Fixed April 15.

## Apple Notes failures

- **Em-dashes (—) in note titles break AppleScript lookup.** The note's `name` property in CoreData differs from the UI display when special chars are present.
- **Bold-styled first lines also fail lookup.**
- **Long first lines get truncated in the stored name — partial match fails.**
- **SOLUTION: Migrated to GitHub repo for all handoff docs.** Apple Notes is now personal/legacy only. GitHub raw URLs + web_fetch = reliable across all Claude sessions.

## Gmail API

- **OR queries are unreliable.** Use one query per sender, deduplicate with Python set().
- **Gmail REST API requires OAuth 2.0 only** — app passwords don't work.
- **Pagination at 500 results** — use nextPageToken.
- **Gmail's `after:` search is day-grain only** — must verify `internalDate` timestamp for hour-level precision.

## Stripe

- **InvoiceItem must be created BEFORE Invoice.create().** Order: Customer.create() → InvoiceItem.create() → Invoice.create().
- **auto_advance=True** means Stripe sends the invoice automatically. Don't manually send after creating.
- **Duplicate customer cleanup is manual.** Jane created a polluted duplicate for Laura (cus_UKnzYxWOFhngOE) that had to be manually deleted.
- **Live key vs test key:** ALWAYS use STRIPE_TEST_SECRET_KEY during development. Live key = real money.

## WordPress

- **REST API /settings silently accepts unknown keys with 200.** Doesn't mean the setting was saved.
- **deploy_widget.py only ships widget.js** — PHP files on VPS are dead code, not deployed.

## General architecture principles (Travis-defined)

1. **Root fixes only, never patches.** If something is broken, fix the cause, not the symptom.
2. **Claude does the work, not Travis.** Travis presents the idea and general flow; Claude builds.
3. **Don't start autonomous businesses before Jane can handle simple transactions.** Etsy was deferred for this reason.
4. **Manual review is a feature, not a bug.** NDA signature review is permanent until DocuSign. First-3-customer draft review for delivery emails. Trust is earned incrementally.
5. **One VPS terminal command at a time** via copy-paste. Claude handles multi-step autonomously only when using Claude Code (not this chat).

## Technical learnings

- **filelock library** prevents concurrent engine runs — import from PyPI, not stdlib.
- **Atomic file writes:** write to .tmp then rename. Prevents corruption on crash.
- **Heredoc pastes from this chat to terminal** sometimes mangle characters. The terminal paste buffer drops newlines or smashes lines together. Use `cat >` with EOF markers, verify the output.
- **Python `source` behavior:** `source file.py | head` runs source in a subshell — variables don't persist. Use `set -a; source file; set +a` in the current shell.
- **The `<system>` block** appearing at the end of Travis's messages in Claude.ai is benign infrastructure echo of Claude's deferred tool definitions. Confirmed harmless April 15 via incognito test. Ignore.

## Stripe object access (confirmed April 16)

- **Stripe objects are NOT dicts.** Use `invoice.status` not `invoice.get("status")`. Using `.get()` throws `AttributeError: get`.
- This bit us TWICE in one night: once on Laura's invoice status check, once on payment_watch.py.
- Rule: ALWAYS use attribute access for Stripe object fields. NEVER use `.get()`.
- Similarly: `invoice.amount_paid` not `invoice.get("amount_paid", 0)`. Use `or 0` for None safety: `invoice.amount_paid or 0`.

## Invoice creation order (confirmed April 16)

- **Create Invoice as draft FIRST, then add InvoiceItem with explicit `invoice=` param, then finalize.**
- The old pattern (create InvoiceItem, then Invoice.create with auto_advance=True) resulted in empty $0 invoices because the item didn't attach.
- Correct pattern:
  1. `stripe.Invoice.create(customer=cust_id, auto_advance=False, ...)`
  2. `stripe.InvoiceItem.create(customer=cust_id, invoice=inv_id, amount=..., ...)`
  3. `stripe.Invoice.finalize_invoice(inv_id, auto_advance=True)`
