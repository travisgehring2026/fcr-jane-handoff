# FCR / Jane Handoff — Index

**Last updated:** April 16, 2026 ~4pm PT
**Purpose:** Source of truth for all FCR/Jane context. Every new Claude session starts by reading this file.

## How to use this repo

1. **New Claude session:** Say "pull Jane handoff" or "pull handoff docs"
2. **End of session:** Claude updates relevant docs, commits, pushes
3. **Travis reads:** Browse at https://github.com/travisgehring2026/fcr-jane-handoff

## Document map

| File | What it is |
|---|---|
| 00_INDEX.md | This file. Master TOC + status snapshot. |
| 01_CORE_HANDOFF.md | System state, rules, infrastructure, credentials locations |
| 02_ROADMAP.md | Phase order, priorities, revenue ladder, NOT NOW list |
| 03_PHASE0_SPEC.md | Universal Customer Lifecycle architecture + build status |
| 04_LAURA_DEAL.md | Laura Moser deal record (closed) |
| 05_PRODUCTS.md | Product catalog: pricing, delivery, config paths |
| 09_LESSONS_LEARNED.md | Rules, gotchas, anti-patterns from real failures |
| 10_SOCIAL_MEDIA_SPEC.md | Social media integration spec (build our own, no Composio) |
| 11_POM_EMAIL_CAMPAIGN.md | Peace of Mind email blast spec + results |
| CLAUDE_INSTRUCTIONS.md | How future Claude should use this repo |
| sessions/ | Per-session logs (append-only) |

## Current status snapshot

- **Phase 0 (Jane reliability):** ALL 9 LIFECYCLE STAGES BUILT AND TESTED. Awaiting cron setup + 3 end-to-end cron tests to declare proven.
- **POM Email Campaign:** EXECUTED — 235 emails sent April 16, 0 errors. Suppression list at 251. pom_worker.py reverted to normal 5/day rolling schedule.
- **LeadGate:** PAUSED — outreach suspended pending Phase 0 cron tests
- **Laura Moser:** CLOSED — $175 paid, $175 auto-bills April 28, package delivered
- **Etsy:** DEFERRED — 60+ days clean bridge + LeadGate 3 clients first
- **Social Media:** SPEC COMPLETE — build our own Facebook/Instagram/TikTok (Phase 2). No Composio.
- **Bridge v2:** LIVE — crash loop fixed April 16 (stale restart task draft), 51 stale drafts cleaned
- **VPS:** 138.68.26.63 — all services green
- **Handoff system:** This repo is source of truth. Apple Notes deprecated.
- **Stripe TEST key:** Configured. Test customer cus_ULMIVQzyty9vEA exists.

## Quick reference

- VPS SSH: ssh root@138.68.26.63
- Env file: /root/fcr.env (MUST use set -a; source /root/fcr.env; set +a)
- Lifecycle engine: /root/jane-workers/lifecycle/engine.py
- Products: /root/jane-workers/products/*.json
- Customers: /root/jane-workers/customers/{slug}/state.json
- Handoff repo on VPS: /root/jane-workers/handoff-repo/
- POM suppression list: /root/jane-workers/logs/pom_suppression.json (251 entries)
- RepairDesk API base: https://api.repairdesk.co/api/web/v1

## What to do next session

1. Add lifecycle engine to cron (every 2 min)
2. Clean up test customer, inject 3 fresh test customers
3. Let cron process all 3 end-to-end without manual intervention
4. Declare Phase 0 foundation proven
5. Resume LeadGate outreach
6. Monitor POM campaign replies (unsubscribes, inquiries)

## Claude instructions

1. web_fetch this file: https://raw.githubusercontent.com/travisgehring2026/fcr-jane-handoff/main/00_INDEX.md
2. Then fetch 01_CORE_HANDOFF.md and 02_ROADMAP.md
3. Then fetch whatever is relevant to the current task
4. At session end: update docs, commit, push via VPS SSH
