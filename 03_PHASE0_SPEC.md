# Phase 0 — Universal Customer Lifecycle (UCL) Spec

**Last updated:** April 15, 2026 ~7pm PT
**Status:** 3 of 9 stages built + tested. INVOICE is next.

## Purpose

One workflow engine handles every paying customer — Repair-Helper today, LeadGate/LocalIQ/Etsy later. Product-specific behavior lives in JSON config files, not code. Adding a new product = adding a JSON file.

## The 9 Stages

```
1. SIGNAL        → New customer detected
2. QUALIFY        → Right product? Right price? Approval needed?
3. CONTRACT       → NDA / TOS (skip if N/A)
4. INVOICE        → Stripe customer + invoice
5. PAYMENT_WATCH  → Monitor for payment
6. PROVISION      → Acquire credentials (skip if N/A)
7. DELIVER        → Send deliverable
8. ACTIVATE       → Onboarding, check-ins
9. STEWARD        → Ongoing: subscription health, churn, billing
```

## Build Status

| Stage | Status | Key behavior |
|---|---|---|
| SIGNAL | ✅ Tested | Validates customer name + email, creates state file, WhatsApp alert |
| QUALIFY | ✅ Tested | Checks price range, payment schedule, email format. Auto-approves if within template, requests Travis approval if outside |
| CONTRACT | ✅ Tested | Generates NDA from template, emails customer, polls for signed return, forwards to Travis for manual review, waits for APPROVED/REJECTED |
| INVOICE | ⬜ Next | Stripe customer + invoice creation |
| PAYMENT_WATCH | ⬜ | Poll Stripe for paid status |
| PROVISION | ⬜ | Skipped for Repair-Helper (no credentials needed) |
| DELIVER | ⬜ | Email deliverable zip. Draft-first-3-customers gating |
| ACTIVATE | ⬜ | Day-7 check-in, day-30 referral ask. Draft-first-3 gating |
| STEWARD | ⬜ | Subscription health, failed payment handling |

## Architecture

### Engine
- File: `/root/jane-workers/lifecycle/engine.py`
- Runs via manual invocation (NOT on cron yet — will add after all stages built)
- Walks `/root/jane-workers/customers/*/state.json`
- For each non-terminal customer whose `next_check_at` has passed: loads product config, dispatches to stage handler
- File-locked via `filelock` library to prevent concurrent runs
- Atomic state writes via .tmp + rename

### Stage handler contract
Every handler implements `run(state, product_config) -> state`:
- Idempotent — safe to re-run
- Sets `stage_status`: `in_progress`, `complete`, `blocked`, `failed`
- On `complete`: advances to next stage
- On `blocked`: sets `next_check_at` to future time
- On `failed`: sets `terminal=True` with reason
- Appends to history.json + notes.md
- Sends WhatsApp alert on transitions

### Customer state file (`state.json`)
```json
{
  "customer_slug": "laura_moser_geek2go",
  "customer": {"name", "company", "email", "phone", "channel_origin"},
  "product_id": "repair_helper_license",
  "deal": {"price_total_cents", "payment_schedule", "notes"},
  "stage": "CONTRACT",
  "stage_status": "blocked",
  "stripe": {"customer_id", "invoice_ids", "subscription_id"},
  "approvals": [...],
  "errors": [...],
  "alerts_sent": [...],
  "completed_stages": ["SIGNAL", "QUALIFY"],
  "terminal": false,
  "terminal_reason": null
}
```

### Product config (`/root/jane-workers/products/*.json`)
Each product declares: which stages apply, pricing rules, payment schedules, contract templates, delivery methods, activation tasks, stewardship rules.

Currently only `repair_helper_license.json` exists ($500 standard, $450-$550 auto-approved, single immediate payment).

### Approval flow
Cross-cutting concern. Any stage can call `request_approval()` to:
1. Send structured email to Travis (subject: `APPROVAL NEEDED: {type} — {company}`)
2. Set state to blocked
3. On subsequent ticks, check Gmail for APPROVED/REJECTED reply
4. Now supports email attachments (used by CONTRACT to forward signed NDAs)

### Manual NDA signature review
PERMANENT until DocuSign integration. Every NDA return gets forwarded to Travis with the customer's PDF attached. Travis replies APPROVED or REJECTED. On REJECTED, customer is emailed asking to re-sign.

### Draft-first-N gating
DELIVER and ACTIVATE stages have `draft_first_n_customers: 3` in product config. First 3 customers get emails as drafts for Travis to review before sending. After 3 successful reviews, switch to fully autonomous.

## Key files on VPS

```
/root/jane-workers/lifecycle/
├── engine.py
├── signal_inject.py           (CLI: --name --company --email --product --channel)
├── stages/signal.py
├── stages/qualify.py
├── stages/contract.py
├── lib/state.py
├── lib/whatsapp.py
├── lib/approval.py
├── lib/gmail.py
├── lib/nda_generator.py
├── lib/state_schema.json
├── templates/repair_helper_nda.docx
└── templates/repair_helper_nda_email.txt
```

## Test results (April 15)

| Test | Scenario | Result |
|---|---|---|
| 1 | Happy path ($500, default terms) | ✅ SIGNAL → QUALIFY → CONTRACT in 2 ticks |
| 2 | Price out of range ($750) | ✅ Approval email sent, customer blocked |
| 3 | Missing email (hard fail) | ✅ Terminated cleanly |
| 4a | CONTRACT NDA generation + send | ✅ Real NDA emailed to travisgehring@me.com |
| 4b | CONTRACT NDA return detection | ✅ Customer reply with PDF found, downloaded |
| 4c | CONTRACT manual review forward | ✅ PDF forwarded to Travis with attachment |
| 4d | CONTRACT APPROVED → INVOICE | ✅ Travis replied APPROVED, state transitioned |

## Next steps for Phase 0

1. **Add Stripe TEST key** to /root/fcr.env (`STRIPE_TEST_SECRET_KEY=sk_test_...`)
2. **Build INVOICE stage** — Stripe customer + invoice creation
3. Build PAYMENT_WATCH — poll Stripe for paid status
4. Build DELIVER — email + zip attachment + draft-first-3
5. Build ACTIVATE — day-7 + day-30 schedulers
6. Build STEWARD — subscription health monitoring
7. Add engine to cron (every 2 min)
8. Run 3 end-to-end test transactions
9. Declare Phase 0 complete → unblock LeadGate
