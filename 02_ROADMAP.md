# FCR / Jane Roadmap v2

**Last updated:** April 16, 2026 ~4am UTC
**Reordered by Travis April 15.** Etsy DEFERRED. Jane reliability is Phase 0.

## Phase Order

| Phase | Name | Status | Gate |
|---|---|---|---|
| 0 | Jane Reliability (UCL) | 9/9 stages built | None |
| 1 | LeadGate Resume | PAUSED | Phase 0 cron tests pass |
| 2 | FCR Intelligence + Social Media | NOT STARTED | Phase 1 active |
| 3 | Accounting | NOT STARTED | Phase 2 complete |
| 4 | Etsy Passive Income | DEFERRED | 60+ days clean bridge + LeadGate 3 clients |
| 5 | LocalIQ Launch | NOT STARTED | LeadGate 3 paying clients |

## QUICK WIN: Peace of Mind Email Campaign (independent of phases)

**Priority: HIGH — do this next week. Revenue sitting on the table.**

One-time email blast to FCR's FULL RepairDesk customer database promoting the Peace of Mind Plan. Currently Jane only emails customers from the last 90 days. Years of PC repair customers have never heard about POM.

Target: All PC repair customers who authorized email and have not opted out. Exclude business accounts.

Email approach:
- Straightforward but whimsy and eye-catching
- Why POM is better than Norton, McAfee, etc.
- Emphasize: local repair shop you already know and trust
- What the Peace of Mind Plan includes and pricing
- Attach the POM flyer (PDF)
- One-time send only — suppress so nobody gets it twice
- Track recipients, never re-send

Technical approach:
- Extract email list from RepairDesk API (filter: PC repairs, authorized email, not opted out, not business)
- If list is under 500: send via Gmail over 1-2 days
- If list is 500-5000: send in batches of 200-300/day over 1-2 weeks
- If list is 5000+: consider free-tier transactional email service (SendGrid gives 100/day free)
- Record every sent email in a suppression list so campaign never re-fires
- Jane handles the batching and scheduling automatically

Prerequisites:
- RepairDesk API access to pull customer list with email + repair type + opt-in status
- POM flyer PDF uploaded to /root/jane-workers/deliverables/
- Email copy drafted and approved by Travis
- Determine list size to pick sending approach

Build estimate: 1 session (2-3 hours). Most of that is email copy + RepairDesk API integration.

## Phase 0 — Jane Reliability (9/9 STAGES BUILT)

ALL 9 LIFECYCLE STAGES BUILT AND TESTED.

Remaining to declare Phase 0 PROVEN:
1. Add engine to cron (every 2 min)
2. Clean up test customer, inject 3 fresh test customers
3. Let cron process all 3 end-to-end without manual intervention
4. Declare Phase 0 foundation proven
5. Unblock LeadGate

## Phase 1 — LeadGate Resume

Outreach PAUSED April 15. Cron backup at /tmp/cron.bak.20260415.

After Phase 0 cron tests:
- Get Started onboarding pipeline live
- Add leadgate_subscription.json product config to lifecycle engine
- Widget payment flow verified end-to-end
- Resume outreach worker (5/day to Boise)
- First paying client
- Three paying Boise clients = unlocks LocalIQ + starts Etsy clock

## Phase 2 — FCR Intelligence + Social Media (EXPANDED)

### Workstream A: FCR Intelligence
- Closed-loop SEO/Ads/Search Console
- Blog reads Search Console for content ideas
- Revenue Intelligence with Claude review
- /repair-helper/ page rebuild
- Widget GA4 event tracking
- Link GA4 to Google Ads

### Workstream B: Social Media Marketing (build our own, no Composio)

Decision (Travis April 16): Build own Facebook/Instagram/TikTok integrations. No third-party fees. Full IP ownership. Reusable for LocalIQ.

Libraries to build:
- lib/instagram.py — Meta Graph API (photos, stories, reels, insights)
- lib/facebook.py — Meta Graph API (page posts, comments, review shares)
- lib/tiktok.py — TikTok API (video uploads, analytics)

Content pipeline: Jane generates -> Travis approves (email) -> Jane publishes
After 3 weeks of approved posts, Jane posts autonomously

Prerequisites (Travis does): Create Business accounts for Facebook, Instagram, TikTok. Get API credentials. Store in /root/fcr.env.

Build estimate: 8-10 hours across 2-3 sessions
Full spec: see 10_SOCIAL_MEDIA_SPEC.md

## Phase 3 — Accounting (NEW)

- Define data flows (RepairDesk, Stripe, QBD, GBP)
- Automate reconciliation
- Single source of truth dashboard
- Tax-readiness automation
- NOT migrating QuickBooks Desktop to QBO

## Phase 4 — Etsy Passive Income (DEFERRED)

Bar: LeadGate 3+ clients + 60 days clean bridge.

## Phase 5 — LocalIQ Launch

Gated on LeadGate 3 paying clients.
- White-label digital presence management
- $29.99 audit / $49.99/month / bundle = $128.99
- NOW INCLUDES social media management (reuses FCR's own integrations)
- Target: 50 Boise clients = $2,500/mo recurring

## Grants (parallel track)

- NSF SBIR Phase I — $275k — JUNE 4 DEADLINE
- Verizon Digital Ready — $10k — complete 2 free courses
- Elevate Idaho: tmcknight@rediconnects.org

## Revenue Ladder

| Stream | Status | Potential |
|---|---|---|
| FCR shop revenue | LIVE | $22-24k/mo |
| Peace of Mind (expanded blast) | QUICK WIN — next week | $149.99-249.99/yr per signup |
| Repair-Helper (Laura) | CLOSED | $350 one-time |
| Repair-Helper (standard) | Ready to sell | $500/each |
| Repair-Helper referrals | Waiting on Laura | $500/each |
| LeadGate clients | PAUSED | $278/mo per client |
| LocalIQ clients (incl social) | Not built | $50-129/mo per client |
| NSF SBIR grant | June 4 deadline | $275k one-time |
| Verizon Digital Ready | Courses needed | $10k one-time |
| Etsy passive | Deferred | $50-500/mo |

## NOT NOW List

- No new product ideas (5 phases + quick wins is enough)
- No QBO migration
- No Composio or third-party integration middlemen (build our own)
- No custom CRM/analytics
- No LeadGate scale until 3 paying Boise clients
- No Jane financial ops until Phase 0 cron tests pass
- No touching VPS dead PHP files

## Key dates

- This week: POM email campaign draft + list extraction
- April 22: Bridge midpoint review + Laura check-in email
- April 28: Verify Laura Invoice 2 auto-billed and paid
- May 15: Target for Phase 0 complete + LeadGate resume
- June 4: NSF SBIR submission deadline
