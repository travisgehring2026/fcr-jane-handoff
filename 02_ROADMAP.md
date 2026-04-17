# FCR / Jane — Roadmap

**Last updated:** April 17, 2026
**Principle:** Revenue-generating work first. Infrastructure supports revenue.

---

## Phase Status

| Phase | Status | Revenue Impact |
|---|---|---|
| 0: Jane Reliability (UCL) | ✅ PROVEN | Foundation for all product sales |
| Quick Win: POM Campaign | ✅ EXECUTED | 235 emails sent, monitoring replies |
| 1: LeadGate Resume | ✅ DEPLOYED | Outreach worker live, cron daily 10am MT |
| 2a: Repair-Helper V2 SaaS | ✅ LIVE | $39.99-79.99/mo recurring per customer |
| 2b: Social Media | SPEC COMPLETE | Marketing channel for all products |
| 3: Accounting | NOT STARTED | QuickBooks automation |
| 4: Etsy | DEFERRED | Passive income (60+ days clean bridge first) |
| 5: LocalIQ | NOT STARTED | National expansion |

---

## Retired Systems

| System | Status | Replaced By |
|---|---|---|
| Discord Repair-Helper Bot | DEAD — killed, masked, file renamed .RETIRED | Repair-Helper V2 SaaS dashboard |
| Claude Bridge (gmail drafts) | DEAD — killed, file renamed .RETIRED | WhatsApp commands to Jane + Claude Code direct VPS access |

---

## Repair-Helper V2 SaaS — LIVE ✅

### What It Is
Hosted AI diagnostic assistant for repair shops. No code exposed. Customers log into a web dashboard, ask diagnostic questions, get structured responses powered by Claude. FCR runs everything on our VPS using our Anthropic API key. Kill switch = disable login.

### URLs
- Marketing: tryleadgate.com/repair-helper.html
- Login: tryleadgate.com/rh/login
- Dashboard: tryleadgate.com/rh/dashboard
- Admin: tryleadgate.com/rh/admin (Travis only — no UI link for other users)
- Forgot Password: tryleadgate.com/rh/forgot-password
- Stripe Webhook: tryleadgate.com/api/rh-stripe-webhook

### Pricing
- Starter: $39.99/mo — 500 conversations, up to 3 tech logins
- Pro: $79.99/mo — 1,000 conversations, unlimited logins
- No setup fee. Cancel anytime.
- V1 ($500 code package) RETIRED. Laura grandfathered at $175/mo.

### Margins
- ~$0.016/query Haiku, ~$0.03/query Sonnet (images/complex)
- 70/30 Haiku/Sonnet smart routing
- Average shop (550 queries/mo): ~$8.80/mo API cost
- Margin: 78-92% at $39.99/mo

### Features — ALL BROWSER-VERIFIED
1. Login/logout with team member support
2. Diagnostic chat with structured output (confidence %, parts, customer scripts)
3. Conversation history sidebar with search box
4. Image upload (camera icon → Claude Sonnet vision)
5. Knowledge base (44 cases seeded from Discord, auto-collecting)
6. Resolution tracking ("that fixed it" → saves confirmed fix)
7. Upgrade modal at conversation limit → one-click Stripe Pro checkout
8. Self-service password reset (/rh/forgot-password)
9. Admin panel with API cost tracking (Yesterday/MTD/YTD + per-shop)
10. Kill switches in admin (Revoke/Activate per shop)
11. Welcome email (approved professional template from Jane)
12. Auto-suspension on Stripe cancellation (TESTED — login blocked immediately)
13. Forgot password link on login page
14. Mobile responsive (sidebar hides on mobile)

### Stripe
- Product: prod_ULhuwngmDetHAu
- Starter: price_1TN0V0ImCdFEvPjeKMufuAmj ($39.99/mo)
- Pro: price_1TN0V1ImCdFEvPjerJcSukaO ($79.99/mo)
- Webhook secret: STRIPE_RH_WEBHOOK_SECRET in /root/fcr.env
- Events: checkout.session.completed, customer.subscription.deleted, invoice.payment_failed

### Signup Flow (Fully Automated)
1. Customer fills form at marketing page
2. POST /api/repair-helper-signup → creates inactive account + Stripe checkout
3. Customer redirected to Stripe → pays $39.99/mo
4. Stripe webhook fires → account activated → credentials email sent from Jane
5. Customer logs in → forced password change → starts diagnosing
6. Cancel in Stripe → webhook fires → account deactivated → login blocked
7. WhatsApp alerts to Travis at every step

### FCR Master Account
- travis@fourcornersrepair.com (admin, unlimited, password: Repairman1!)
- will@fourcornersrepair.com (technician, unlimited)
- jonathan@fourcornersrepair.com (store manager, unlimited)

### Knowledge Base
- 44 cases seeded from Discord history
- Devices: ps5(6), iphone(5), ipad(5), windows(4), dell(4), laptop(3), xbox(2), macbook(1), others
- Every diagnostic logged (resolved or not)
- Resolution tracking: tech says "that fixed it" → case marked resolved
- Similar cases pulled as context before every Claude query
- Grows smarter with every shop, every diagnostic

### Architecture
- Backend: /root/leadgate/repair_helper_v2/ (auth, accounts, diagnose, knowledge, routes)
- Templates: /root/leadgate/repair_helper_v2/templates/
- Data: /root/leadgate/rh_data/shops/{slug}/
- Knowledge: /root/leadgate/rh_data/knowledge/cases/{device}/
- Cost log: /root/leadgate/rh_data/cost_log.json
- Caddy: tryleadgate.com → reverse_proxy 127.0.0.1:5050

### ⚠️ Critical Notes
- Caddy proxies to Flask on port 5050 (NOT 18789 openclaw gateway)
- If Caddy reverts to 18789, dashboard features will appear missing
- Flask uses dotenv to load /root/fcr.env (all API keys, Stripe secrets)
- Session cookies: SameSite=Lax, HttpOnly=True, Secure=False (Caddy handles HTTPS)

---

## LeadGate Outreach — DEPLOYED ✅

- Verticals: HVAC, Plumbing, Auto, Vet (4 active only)
- Locations: Boise, Meridian, Nampa, Eagle, Caldwell, Star, Kuna, Garden City
- Daily limit: 200 emails
- Suppression: 21 days
- Cron: Daily at 4pm UTC (10am Mountain)
- API budget: $175/mo max
- Prospects: 530 discovered (134 in active verticals)
- Worker: /root/jane-workers/leadgate_outreach_worker.py
- First batch fires April 17

---

## POM Campaign — EXECUTED ✅

- 235 HTML emails sent April 16, 0 errors
- Professional template with POM flyer PDF attached
- Subject: "Your local repair shop now offers something Norton can't"
- Hours: Mon-Sat 10am-6pm, Sun 12pm-6pm
- Rolling: 5/day, 90-day schedule via pom_worker.py

---

## Laura Moser — CLOSED ✅

- Invoice 1: $175 PAID
- Invoice 2: $175 auto-bills April 28
- V1 code package delivered. Grandfathered.
- Upgrade offer when V2 has 5+ customers.

---

## Phase 2b: Social Media — SPEC COMPLETE

- Build own Facebook/Instagram/TikTok integrations
- Spec: 10_SOCIAL_MEDIA_SPEC.md
- Prerequisite: Travis creates Business accounts + gets API credentials

---

## Active Cron Jobs

| Schedule | Job | Purpose |
|---|---|---|
| */2 * * * * | lifecycle/engine.py | Lifecycle stage processing |
| 0 16 * * * | leadgate_outreach_worker.py | LeadGate outreach (10am MT) |
| 0 15 * * * | repairdesk_worker.py | RepairDesk data sync |
| (rolling) | pom_worker.py | POM email campaign (5/day) |

---

## Active Services

| Service | Port | Purpose |
|---|---|---|
| Flask (app.py) | 5050 | LeadGate + Repair-Helper V2 |
| Caddy | 443 | HTTPS → 5050 proxy |
| OpenClaw Gateway | 18789 | Legacy — NOT used for tryleadgate.com anymore |

---

## Upcoming Dates

| Date | Item |
|---|---|
| April 17 | LeadGate outreach first batch fires (cron) |
| April 22 | Bridge midpoint review |
| April 28 | Laura Invoice 2 auto-bill |
| May 1 | First month V2 cost analysis |
| June 4 | NSF SBIR submission deadline |

---

## NOT NOW (Closed / Deferred)

- Game console add-on pricing (closed)
- NSD school district (closed)
- Composio (rejected)
- Apple Notes for handoff (deprecated — GitHub is source of truth)
- Repair-Helper V1 code sales (retired)
- Etsy (deferred — 60+ days clean bridge first)
- Claude Bridge (retired — WhatsApp + Claude Code replaced it)
- Discord Repair-Helper Bot (retired — V2 SaaS replaced it)
