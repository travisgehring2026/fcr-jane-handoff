# FCR / Jane — Roadmap

**Last updated:** April 16, 2026 ~9pm PT
**Principle:** Revenue-generating work first. Infrastructure supports revenue.

## Phase Status

| Phase | Status | Revenue impact |
|---|---|---|
| 0: Jane Reliability (UCL) | PROVEN | Foundation for all product sales |
| Quick Win: POM Campaign | EXECUTED | 235 emails sent, monitoring replies |
| 1: LeadGate Resume | DEPLOYED | Outreach worker live, cron daily 10am MT |
| 2a: Repair-Helper V2 SaaS | LIVE | $39.99-79.99/mo recurring per customer |
| 2b: Social Media | SPEC COMPLETE | Marketing channel for all products |
| 3: Accounting | NOT STARTED | QuickBooks automation |
| 4: Etsy | DEFERRED | Passive income (60+ days clean bridge first) |
| 5: LocalIQ | NOT STARTED | National expansion |

## Repair-Helper V2 SaaS — LIVE

### What it is
Hosted AI diagnostic assistant for repair shops. No code exposed. Customers log into a web dashboard, ask diagnostic questions, get structured responses powered by Claude. We run everything on our VPS using our Anthropic key.

### URLs
- Marketing: tryleadgate.com/repair-helper.html
- Login: tryleadgate.com/rh/login
- Dashboard: tryleadgate.com/rh/dashboard
- Admin: tryleadgate.com/rh/admin (Travis only, no UI link)
- Stripe webhook: tryleadgate.com/api/rh-stripe-webhook

### Pricing
- Starter: $39.99/mo — 500 conversations, up to 3 tech logins
- Pro: $79.99/mo — 1,000 conversations, unlimited logins
- No setup fee. Cancel anytime.
- V1 ($500 code package) RETIRED. Laura grandfathered.

### API Cost Per Customer
- ~$0.016/query with 70/30 Haiku/Sonnet smart routing
- Average shop (550 queries/mo): $8.80/mo API cost
- Margin: 78-92% at $39.99/mo

### Architecture
- Backend: /root/leadgate/repair_helper_v2/ (auth.py, accounts.py, diagnose.py, knowledge.py, routes.py)
- Templates: /root/leadgate/repair_helper_v2/templates/ (login, dashboard, settings, welcome, admin)
- Shop data: /root/leadgate/rh_data/shops/{slug}/
- Knowledge base: /root/leadgate/rh_data/knowledge/cases/ (44 cases seeded from Discord)
- Stripe: prod_ULhuwngmDetHAu, prices price_1TN0V0ImCdFEvPjeKMufuAmj (starter), price_1TN0V1ImCdFEvPjerJcSukaO (pro)
- Webhook secret: STRIPE_RH_WEBHOOK_SECRET in /root/fcr.env

### Kill Switch
- Login IS the kill switch. Disable account = instant cutoff.
- Admin panel at /rh/admin — Revoke/Activate buttons per shop.
- Stripe cancellation webhook auto-revokes access.
- No code exposed. No API keys for customer to manage.

### FCR Master Account
- travis@fourcornersrepair.com (admin, unlimited)
- will@fourcornersrepair.com (technician, unlimited)
- jonathan@fourcornersrepair.com (store manager, unlimited)
- Discord repair-helper bot: OFFLINE (systemd disabled)

### Knowledge Base
- 44 cases seeded from Discord history
- Every diagnostic logged (resolved or not)
- Resolution tracking: tech says "that fixed it" → case marked resolved
- Similar cases pulled as context for future queries
- Grows smarter with every shop, every diagnostic

### Customer Flow (Fully Automated)
1. Customer visits tryleadgate.com/repair-helper.html
2. Fills out signup form
3. Jane sends Stripe checkout link ($39.99/mo)
4. Customer pays → Stripe webhook fires
5. Jane auto-creates account (email + random password)
6. Credentials emailed to customer
7. Customer logs in, forced password change
8. Uses diagnostic tool (500 conversations/month)
9. Hit limit → upgrade prompt to Pro ($79.99/mo)
10. Cancel Stripe → Jane revokes access immediately

## LeadGate Outreach — DEPLOYED

### Configuration
- Verticals: HVAC, Plumbing, Auto, Vet (4 active only)
- Locations: Boise, Meridian, Nampa, Eagle, Caldwell, Star, Kuna, Garden City
- Daily limit: 200 emails
- Suppression: 21 days
- Cron: Daily at 4pm UTC (10am Mountain)
- API budget: $175/mo max (out of $200 free credit)
- Prospects discovered: 530 valid emails (134 in active verticals)

### Files
- Worker: /root/jane-workers/leadgate_outreach_worker.py
- Cache: /root/jane-workers/logs/leadgate_prospects_cache.json
- Suppression: /root/jane-workers/logs/leadgate_outreach_suppression.json

## POM Campaign — EXECUTED

- 235 HTML emails sent April 16, 0 errors
- Professional HTML template with POM flyer PDF attached
- Subject: "Your local repair shop now offers something Norton can't"
- Suppression list: 251 entries
- pom_worker.py reverted to 5/day, 90-day rolling schedule
- Hours: Mon-Sat 10am-6pm, Sun 12pm-6pm

## Social Media — SPEC COMPLETE (Phase 2b)

- Build own Facebook/Instagram/TikTok integrations (no Composio)
- Spec: 10_SOCIAL_MEDIA_SPEC.md
- Prerequisite: Travis creates Business accounts + gets API credentials

## Laura Moser — CLOSED

- Invoice 1: $175 PAID
- Invoice 2: $175 auto-bills April 28
- V1 code package delivered. Grandfathered.
- Upgrade offer when V2 has 5+ customers.

## NOT NOW

- Game console add-on pricing
- NSD school district
- Composio (rejected)
- Apple Notes for handoff (deprecated)
- Repair-Helper V1 code sales (retired)

## Key Dates

- April 17: LeadGate outreach first batch (cron)
- April 22: Bridge midpoint review
- April 28: Laura Invoice 2 auto-bill
- June 4: NSF SBIR submission deadline
