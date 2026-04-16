# FCR / Jane Handoff — Index

**Last updated:** April 16, 2026 ~3am UTC (~8pm PT April 15)
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
| CLAUDE_INSTRUCTIONS.md | How future Claude should use this repo |
| sessions/ | Per-session logs (append-only) |

## Current status snapshot

- **Phase 0 (Jane reliability):** 7 of 9 lifecycle stages built + tested. STEWARD is the last stage to build. PROVISION is skipped for Repair-Helper.
- **Stages proven:** SIGNAL, QUALIFY, CONTRACT, INVOICE, PAYMENT_WATCH, DELIVER, ACTIVATE
- **LeadGate:** PAUSED — outreach suspended pending Phase 0 proof
- **Laura Moser:** CLOSED — $175 paid, $175 auto-bills April 28, package delivered
- **Etsy:** DEFERRED — 60+ days clean bridge + LeadGate 3 clients first
- **Bridge v2:** LIVE with SSL retry — deployed April 15 22:41 UTC
- **VPS:** 138.68.26.63 — all services green
- **Handoff system:** GitHub repo (this repo) is source of truth. Apple Notes deprecated.
- **Stripe TEST key:** Configured in /root/fcr.env. Test customer cus_ULMIVQzyty9vEA exists.

## Quick reference

- **VPS SSH:** ssh root@138.68.26.63
- **Env file:** /root/fcr.env (MUST use set -a; source /root/fcr.env; set +a)
- **Lifecycle engine:** /root/jane-workers/lifecycle/engine.py
- **Products:** /root/jane-workers/products/*.json
- **Customers:** /root/jane-workers/customers/{slug}/state.json
- **Handoff repo on VPS:** /root/jane-workers/handoff-repo/

## What to do next session

1. Build STEWARD stage (lightweight for Repair-Helper)
2. Add engine to cron (every 2 min)
3. Clean up test customer, inject 3 fresh test customers
4. Let cron process all 3 end-to-end without manual intervention
5. Declare Phase 0 foundation proven
6. Resume LeadGate outreach

## Claude instructions

1. web_fetch this file: https://raw.githubusercontent.com/travisgehring2026/fcr-jane-handoff/main/00_INDEX.md
2. Then fetch 01_CORE_HANDOFF.md and 02_ROADMAP.md
3. Then fetch whatever is relevant to the current task
4. At session end: update docs, commit, push via VPS SSH
