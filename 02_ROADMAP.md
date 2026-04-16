# FCR / Jane Roadmap v2

**Last updated:** April 16, 2026 ~3am UTC
**Reordered by Travis April 15.** Etsy DEFERRED. Jane reliability is Phase 0.

## Phase Order

| Phase | Name | Status | Gate |
|---|---|---|---|
| 0 | Jane Reliability (UCL) | 7/9 stages built | None |
| 1 | LeadGate Resume | PAUSED | Phase 0 complete |
| 2 | FCR Intelligence | NOT STARTED | Phase 1 active |
| 3 | Accounting | NOT STARTED | Phase 2 complete |
| 4 | Etsy Passive Income | DEFERRED | 60+ days clean bridge + LeadGate 3 clients |
| 5 | LocalIQ Launch | NOT STARTED | LeadGate 3 paying clients |

## Phase 0 — Jane Reliability (ACTIVE)

**Proof bar (Travis verbatim):**
> "Jane recognizes a customer is ready to sign up OR is given a verbal we have a customer, takes that information, and runs the process from beginning to end without our intervention. 100% autonomous."

**UCL Build Status:**

| Stage | Status | What it does |
|---|---|---|
| 1. SIGNAL | BUILT + TESTED | Detect new customer, create state file |
| 2. QUALIFY | BUILT + TESTED | Check price/terms, auto-approve or request Travis approval |
| 3. CONTRACT | BUILT + TESTED | Generate NDA, send, detect signed return, forward to Travis for manual review |
| 4. INVOICE | BUILT + TESTED | Create Stripe customer + invoice (test/live key routing) |
| 5. PAYMENT_WATCH | BUILT + TESTED | Poll Stripe for paid status, detect payment |
| 6. PROVISION | SKIPPED | Not needed for Repair-Helper (no credentials to acquire) |
| 7. DELIVER | BUILT + TESTED | Email deliverable zip, draft-first-3 gating, MD5 verification |
| 8. ACTIVATE | BUILT + TESTED | Day-7 check-in, day-30 referral ask, draft-first-N gating |
| 9. STEWARD | NEXT | Subscription health, ongoing monitoring (last stage) |

**Remaining to complete Phase 0:**
1. Build STEWARD stage (lightweight for Repair-Helper one-time payments)
2. Add engine to cron (every 2 min)
3. Run 3 end-to-end test transactions via cron (no manual intervention)
4. Declare Phase 0 foundation proven
5. Unblock LeadGate

**Key safety features proven:**
- Manual NDA signature review (permanent until DocuSign)
- Draft-first-3-customers gating on DELIVER and ACTIVATE
- Test/live Stripe key routing by customer slug
- WhatsApp alerts on every stage transition
- Approval flow with email reply parsing (APPROVED/REJECTED)
- Deliverable MD5 verification before sending

## Phase 1 — LeadGate Resume

Outreach PAUSED April 15. Cron backup at /tmp/cron.bak.20260415.

After Phase 0:
- Get Started onboarding pipeline live
- Widget payment flow verified end-to-end
- Resume outreach worker (5/day to Boise)
- First paying client
- Three paying Boise clients = unlocks LocalIQ + starts Etsy clock

## Phase 2 — FCR Intelligence (bumped from Phase 5)

- Closed-loop SEO/Ads/Search Console
- Blog reads Search Console for content ideas
- Revenue Intelligence with Claude review
- /repair-helper/ page rebuild
- Widget GA4 event tracking
- Link GA4 to Google Ads

## Phase 3 — Accounting (NEW)

- Define data flows (RepairDesk, Stripe, QBD, GBP)
- Automate reconciliation
- Single source of truth dashboard
- Tax-readiness automation
- NOT migrating QuickBooks Desktop to QBO

## Phase 4 — Etsy Passive Income (DEFERRED)

Travis rationale: "Why would we start an autonomous business when Jane can't handle LeadGate?"
Bar: LeadGate 3+ clients running smoothly + 60 days clean bridge operation.

## Phase 5 — LocalIQ Launch

Gated on LeadGate 3 paying clients.
- White-label digital presence management
- $29.99 audit / $49.99/month / bundle = $128.99
- Target: 50 Boise clients = $2,500/mo recurring

## Grants (parallel track)

- NSF SBIR Phase I — $275k — JUNE 4 DEADLINE
- Verizon Digital Ready — $10k — complete 2 free courses
- Elevate Idaho: tmcknight@rediconnects.org

## Revenue Ladder

| Stream | Status | Potential |
|---|---|---|
| FCR shop revenue | LIVE | $22-24k/mo |
| Repair-Helper (Laura) | CLOSED | $350 one-time |
| Repair-Helper (standard) | Ready to sell | $500/each |
| Repair-Helper referrals | Waiting on Laura | $500/each |
| LeadGate clients | PAUSED | $278/mo per client |
| LocalIQ clients | Not built | $50/mo per client |
| NSF SBIR grant | June 4 deadline | $275k one-time |
| Verizon Digital Ready | Courses needed | $10k one-time |
| Etsy passive | Deferred | $50-500/mo |

## NOT NOW List

- No new product ideas (5 phases is enough)
- No QBO migration
- No custom CRM/analytics
- No LeadGate scale until 3 paying Boise clients
- No Jane financial ops until Phase 0 proven
- No touching VPS dead PHP files

## Key dates

- April 22: Bridge midpoint review + Laura check-in email
- April 28: Verify Laura Invoice 2 auto-billed and paid
- May 15: Target for Phase 0 complete + LeadGate resume
- June 4: NSF SBIR submission deadline
