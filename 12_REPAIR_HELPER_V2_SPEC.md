# Repair-Helper V2 SaaS — Technical Spec

**Last updated:** April 16, 2026 ~9pm PT
**Status:** LIVE — Full platform deployed and operational.

## Product Overview

Hosted AI diagnostic assistant for repair shops. No code exposed. Customers log into a web dashboard, ask diagnostic questions, get structured responses. FCR runs everything on VPS using our Anthropic API key.

## Pricing

| Tier | Price | Conversations | Tech Logins |
|---|---|---|---|
| Starter | $39.99/mo | 500/month | Up to 3 |
| Pro | $79.99/mo | 1,000/month | Unlimited |
| Master (FCR) | Free | Unlimited | Unlimited |

No setup fee. Cancel anytime. V1 ($500 code) retired.

## Technical Architecture

### Backend Files (/root/leadgate/repair_helper_v2/)
- auth.py — Login, sessions, password hashing, team member support, login_required decorator
- accounts.py — Stripe integration, account CRUD, conversation limits, monthly reset
- diagnose.py — Diagnostic engine, Claude API calls, smart model routing, image support
- knowledge.py — Knowledge base storage, case matching, resolution tracking
- routes.py — All Flask routes (Blueprint registered in app.py)

### Templates (/root/leadgate/repair_helper_v2/templates/)
- login.html — Dark theme login page
- dashboard.html — Diagnostic chat interface with conversation history sidebar
- settings.html — Password change, subscription info
- welcome.html — Post-payment welcome page
- admin.html — Admin panel (Travis only, no UI link)

### Data (/root/leadgate/rh_data/)
- shops/{slug}/account.json — Account config, Stripe IDs, conversation count
- shops/{slug}/config.json — Shop customization (devices, SOPs)
- shops/{slug}/conversations/ — Per-session JSON conversation logs
- shops/{slug}/logins/ — Team member login files
- knowledge/cases/{device}/ — Anonymized solved cases by device type
- knowledge/index.json — Knowledge base stats

### Stripe
- Product: prod_ULhuwngmDetHAu
- Starter: price_1TN0V0ImCdFEvPjeKMufuAmj ($39.99/mo)
- Pro: price_1TN0V1ImCdFEvPjerJcSukaO ($79.99/mo)
- Webhook: whsec_ivmv... → /api/rh-stripe-webhook
- Events: checkout.session.completed, customer.subscription.deleted, invoice.payment_failed

### Diagnostic Engine
- System prompt: Repair-Helper identity, structured output format, confidence %, escalation points, customer scripts, resolution tracking
- Smart model routing: Haiku for simple queries, Sonnet for board-level/complex + images
- Knowledge base context: top 3 similar cases injected before every query
- Resolution detection: keywords trigger case marking as resolved
- Image support: base64 → Claude vision API (always Sonnet for images)
- Conversation memory: per-session, stored as JSON

### Security
- Passwords: SHA-256 + salt (hash_password function)
- Sessions: Flask signed cookies
- Admin: server-side email check (travis@fourcornersrepair.com only)
- Kill switch: admin panel Revoke button → account.active = false → instant cutoff

## Knowledge Base

### Current State
- 44 cases seeded from Discord repair-helper bot history
- Organized by device: ps5 (6), iphone (5), ipad (5), windows (4), dell (4), laptop (3), xbox (2), macbook (1), and others
- Every diagnostic query logged (resolved or not)
- Resolution tracking: tech confirms fix → case updated with solution

### How It Works
1. Tech asks question
2. Engine extracts device type + symptoms
3. Knowledge base queried for similar cases (top 3)
4. Similar cases injected into Claude prompt as context
5. Claude responds with structured diagnostic
6. Query + response logged to knowledge base
7. If tech confirms resolution → case marked as resolved with fix details

### Growth Model
- Every shop contributes anonymized cases
- Every diagnostic improves future answers
- 10 shops × 25 queries/day = 250 new cases/day
- After 6 months: 45,000+ cases
