# FCR / Jane — Roadmap

**Last updated:** April 17, 2026 ~3am UTC (April 16 ~8pm PT)
**Principle:** Revenue-generating work first. Infrastructure supports revenue.

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

## Repair-Helper V2 SaaS — LIVE ✅

### What It Is
Hosted AI diagnostic assistant for repair shops. No code exposed. Customers log into a web dashboard, ask diagnostic questions, get structured responses powered by Claude. FCR runs everything on our VPS using our Anthropic API key. Kill switch = disable login.

### URLs
- **Marketing:** tryleadgate.com/repair-helper.html
- **Login:** tryleadgate.com/rh/login
- **Dashboard:** tryleadgate.com/rh/dashboard
- **Admin:** tryleadgate.com/rh/admin (Travis only — no UI link for other users)
- **Forgot Password:** tryleadgate.com/rh/forgot-password
- **Stripe Webhook:** tryleadgate.com/api/rh-stripe-webhook

### Pricing
- **Starter:** $39.99/mo — 500 conversations, up to 3 tech logins
- **Pro:** $79.99/mo — 1,000 conversations, unlimited logins
- No setup fee. Cancel anytime.
- V1 ($500 code package) RETIRED. Laura grandfathered at $175/mo.

### API Cost Per Customer
- ~$0.016/query with Haiku (simple), ~$0.03/query with Sonnet (complex/images)
- 70/30 Haiku/Sonnet smart routing
- Average shop (550 queries/mo): ~$8.80/mo API cost
- **Margin: 78-92% at $39.99/mo**

### Features — ALL VERIFIED IN BROWSER
1. Login/logout with team member support
2. Diagnostic chat with structured output (confidence %, parts, customer scripts)
3. Conversation history sidebar with search box
4. Image upload (camera icon → Claude Sonnet vision)
5. Knowledge base (44 cases seeded from Discord, auto-collecting)
6. Resolution tracking ("that fixed it" → saves confirmed fix)
7. Upgrade modal at conversation limit (one-click to Stripe Pro checkout)
8. Self-service password reset (/rh/forgot-password)
9. Admin panel with cost tracking (Yesterday/MTD/YTD + per-shop breakdown)
10. Kill switches in admin (Revoke/Activate per shop)
11. Welcome email (approved professional template from Jane)
12. Auto-suspension on Stripe cancellation (TESTED — login blocked immediately)

### Stripe
- Product: prod_ULhuwngmDetHAu
- Starter: price_1TN0V0ImCdFEvPjeKMufuAmj ($39.99/mo)
- Pro: price_1TN0V1ImCdFEvPjerJcSukaO ($79.99/mo)
- Webhook: whsec_ivmv... → tryleadgate.com/api/rh-stripe-webhook
- Events: checkout.session.completed, customer.subscription.deleted, invoice.payment_failed

### Signup Flow (Fully Automated)
1. Customer fills form at marketing page
2. POST /api/repair-helper-signup → creates inactive account + Stripe checkout
3. Customer redirected to Stripe → pays $39.99/mo
4. Stripe webhook fires → account activated → credentials email sent
5. Customer logs in → forced password change → starts diagnosing
6. Cancel in Stripe → webhook fires → account deactivated → login blocked

### FCR Master Account
- travis@fourcornersrepair.com (admin, unlimited, password: Repairman1!)
- will@fourcornersrepair.com (technician, unlimited)
- jonathan@fourcornersrepair.com (store manager, unlimited)
- Discord repair-helper bot: OFFLINE (systemd stopped + disabled)
- Welcome emails sent to all 3 from Jane

### Knowledge Base
- 44 cases seeded from Discord history
- Device breakdown: ps5(6), iphone(5), ipad(5), windows(4), dell(4), laptop(3), xbox(2), macbook(1), others
- Every diagnostic logged (resolved or not)
- Resolution tracking: tech says "that fixed it" → case marked resolved
- Similar cases pulled as context before every Claude query
- Grows smarter with every shop, every diagnostic

### Architecture
- Backend: /root/leadgate/repair_helper_v2/ (auth.py, accounts.py, diagnose.py, knowledge.py, routes.py)
- Templates: /root/leadgate/repair_helper_v2/templates/ (login, dashboard, settings, welcome, forgot_password, admin)
- Data: /root/leadgate/rh_data/shops/{slug}/ (per-shop accounts, conversations, logins)
- Knowledge: /root/leadgate/rh_data/knowledge/cases/{device}/ (anonymized cases)
- Cost log: /root/leadgate/rh_data/cost_log.json
- Flask blueprint registered in /root/leadgate/app.py
- Caddy: tryleadgate.com → reverse_proxy 127.0.0.1:5050 (CHANGED from 18789 openclaw gateway)

### Critical Infrastructure Note
- Caddy config changed April 17: now proxies directly to Flask on 5050, bypassing openclaw gateway on 18789
- The openclaw gateway was serving cached/stale HTML — all dashboard features were invisible through it
- If Caddy config is ever reverted to 18789, the dashboard will appear to lose features
- Flask app.py uses dotenv to load /root/fcr.env (Stripe keys, API keys, webhook secret)

---

## LeadGate Outreach — DEPLOYED ✅

### Configuration
- **Verticals:** HVAC, Plumbing, Auto, Vet (4 active only)
- **Locations:** Boise, Meridian, Nampa, Eagle, Caldwell, Star, Kuna, Garden City
- **Daily limit:** 200 emails
- **Suppression:** 21 days
- **Cron:** Daily at 4pm UTC (10am Mountain)
- **API budget:** $175/mo max (out of $200 free credit)
- **Prospects:** 530 discovered (134 in active verticals)

### Files
- Worker: /root/jane-workers/leadgate_outreach_worker.py
- Cache: /root/jane-workers/logs/leadgate_prospects_cache.json
- Suppression: /root/jane-workers/logs/leadgate_outreach_suppression.json

---

## POM Campaign — EXECUTED ✅

- 235 HTML emails sent April 16, 0 errors
- Professional HTML template with POM flyer PDF attached
- Subject: "Your local repair shop now offers something Norton can't"
- Hours: Mon-Sat 10am-6pm, Sun 12pm-6pm
- pom_worker.py: 5/day rolling, 90-day schedule

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
- **Prerequisite:** Travis creates Business accounts + gets API credentials

---

## Upcoming

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
