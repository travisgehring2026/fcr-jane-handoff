# FCR / Jane Roadmap v2

**Last updated:** April 15, 2026 ~7pm PT
**Reordered by Travis April 15.** Key change: Etsy DEFERRED. Jane reliability is Phase 0, prerequisite to everything.

## Phase Order

| Phase | Name | Status | Gate |
|---|---|---|---|
| 0 | Jane Reliability (UCL) | IN PROGRESS | — |
| 1 | LeadGate Resume | PAUSED | Phase 0 complete |
| 2 | FCR Intelligence | NOT STARTED | Phase 1 active |
| 3 | Accounting | NOT STARTED | Phase 2 complete |
| 4 | Etsy Passive Income | DEFERRED | 60+ days clean bridge + LeadGate 3 clients |
| 5 | LocalIQ Launch | NOT STARTED | LeadGate 3 paying clients |

## Phase 0 — Jane Reliability (ACTIVE)

**Proof bar (Travis verbatim April 15):**
> "Jane recognizes a customer is ready to sign up OR is given a verbal we have a customer, takes that information, and runs the process from beginning to end without our intervention. 100% autonomous."

**Concretely — all must pass:**
1. Customer signal received ✅
2. Jane creates Stripe customer + invoice correctly ⬜
3. Jane sends correct delivery email when paid ⬜
4. Jane handles edge cases without silent failure ⬜
5. WhatsApp updates at each milestone ✅
6. Three consecutive successful end-to-end transactions ⬜

**UCL Build Status (9 stages):**
1. SIGNAL ✅ Built + tested
2. QUALIFY ✅ Built + tested (auto-pass + approval-needed)
3. CONTRACT ✅ Built + tested (manual NDA signature review)
4. INVOICE ⬜ **NEXT** — needs Stripe TEST key
5. PAYMENT_WATCH ⬜
6. PROVISION ⬜ (skipped for Repair-Helper)
7. DELIVER ⬜
8. ACTIVATE ⬜
9. STEWARD ⬜

**Remaining build work:** INVOICE → PAYMENT_WATCH → DELIVER → ACTIVATE → STEWARD → cron setup → 3 test transactions → declare complete.

## Phase 1 — LeadGate Resume

Outreach PAUSED April 15. Cron backup at `/tmp/cron.bak.20260415`.

After Phase 0:
- Get Started onboarding pipeline live
- Widget payment flow verified end-to-end
- Resume outreach worker (5/day to Boise prospects)
- First paying client
- Three paying Boise clients = unlocks LocalIQ + Etsy

## Phase 2 — FCR Intelligence (bumped up from Phase 5)

- Closed-loop SEO/Ads/Search Console (bidirectional)
- Blog reads Search Console for content ideas
- Revenue Intelligence — Claude-reviewed, high-confidence
- /repair-helper/ page rebuild
- Widget GA4 event tracking
- Link GA4 to Google Ads for conversion-optimized bidding

## Phase 3 — Accounting (NEW)

- Define data flows (RepairDesk, Stripe, QBD, GBP)
- Automate daily/weekly reconciliation
- Single source of truth dashboard
- Tax-readiness automation
- NOT migrating QuickBooks Desktop to QBO

## Phase 4 — Etsy Passive Income (DEFERRED)

Travis rationale: "Why would we start an autonomous business when Jane can't handle LeadGate?"

Bar to start: LeadGate 3+ clients + 60 days clean bridge.

## Phase 5 — LocalIQ Launch

Gated on LeadGate having 3 paying clients.
- White-label digital presence management
- $29.99 audit / $49.99/month / bundle = $128.99
- Target: 50 Boise clients → $2,500/mo recurring

## Grants (parallel track, time-sensitive)

- NSF SBIR Phase I — $275k — **JUNE 4 DEADLINE**
- Verizon Digital Ready — $10k — complete 2 free courses, apply June-Dec 2026
- Elevate Idaho (free SBIR support): tmcknight@rediconnects.org

## Revenue Ladder

| Stream | Status | Monthly potential |
|---|---|---|
| FCR shop revenue | ✅ Live | $22-24k/mo |
| Repair-Helper (Laura) | ✅ Closed | $350 one-time |
| Repair-Helper referrals | ⏳ Waiting | $350/each |
| LeadGate clients | ⏸ Paused | $278/mo per client |
| LocalIQ clients | ⬜ Not built | $50/mo per client |
| NSF SBIR grant | ⬜ June 4 | $275k one-time |
| Verizon Digital Ready | ⬜ Courses needed | $10k one-time |
| Etsy passive | ⬜ Deferred | $50-500/mo |

## NOT NOW List

- No new product ideas (5 phases is enough)
- No QBO migration
- No custom CRM/analytics that exists as SaaS
- No LeadGate scale until 3 paying Boise clients
- No Jane financial ops until Phase 0 proven
- No touching VPS dead PHP files
