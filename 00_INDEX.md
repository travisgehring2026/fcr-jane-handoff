# FCR / Jane Handoff — Index

**Last updated:** April 15, 2026 ~7pm PT
**Purpose:** Source of truth for all FCR/Jane context. Every new Claude session starts by reading this file.

## How to use this repo

1. **New Claude session:** Say "pull Jane handoff" → Claude reads this index + core docs
2. **End of session:** Claude updates relevant docs, commits, pushes
3. **Travis reads:** Browse at https://github.com/travisgehring2026/fcr-jane-handoff

## Document map

| File | What it is | Update frequency |
|---|---|---|
| `00_INDEX.md` | This file. Master TOC. | Every session |
| `01_CORE_HANDOFF.md` | Current system state, rules, infrastructure | Every session |
| `02_ROADMAP.md` | Phase order, priorities, revenue ladder | When priorities change |
| `03_PHASE0_SPEC.md` | Universal Customer Lifecycle architecture + build status | During Phase 0 build |
| `04_LAURA_DEAL.md` | Laura Moser deal record (closed) | Rarely (only on Apr 28 invoice check) |
| `05_PRODUCTS.md` | Product catalog details + pricing | When products change |
| `06_LEADGATE_BRIEF.md` | LeadGate product brief | When LeadGate work resumes |
| `07_LOCALIQ_BRIEF.md` | LocalIQ product brief | When LocalIQ work begins |
| `08_SERVER_AND_BRIDGE.md` | VPS details, services, bridge architecture | When infra changes |
| `09_LESSONS_LEARNED.md` | Rules, gotchas, anti-patterns from real failures | After every incident |
| `sessions/` | Per-session logs (append-only) | Every session |

## Current status snapshot (April 15, 2026)

- **Phase 0 (Jane reliability):** IN PROGRESS — 3 of 9 lifecycle stages built + tested (SIGNAL, QUALIFY, CONTRACT). INVOICE is next.
- **LeadGate:** PAUSED — outreach suspended pending Phase 0 proof
- **Laura Moser:** CLOSED — $175 paid, $175 auto-bills April 28, package delivered
- **Etsy:** DEFERRED — won't start until LeadGate has 3 paying clients + 60 days clean bridge
- **Bridge v2:** LIVE with SSL retry patch — deployed April 15 22:41 UTC
- **VPS:** 138.68.26.63 (fcr-openclaw, 8GB/$48mo) — all services green
- **Old server 209.38.136.50:** DESTROYED

## Quick reference

- **VPS SSH:** `ssh root@138.68.26.63`
- **Env file:** `/root/fcr.env` (must `set -a; source /root/fcr.env; set +a`)
- **Lifecycle engine:** `/root/jane-workers/lifecycle/engine.py`
- **Products:** `/root/jane-workers/products/*.json`
- **Customers:** `/root/jane-workers/customers/{slug}/state.json`
- **Handoff repo on VPS:** `/root/jane-workers/handoff-repo/`

## Claude instructions

When starting a new session:
1. `web_fetch` this file first: `https://raw.githubusercontent.com/travisgehring2026/fcr-jane-handoff/main/00_INDEX.md`
2. Then `web_fetch` `01_CORE_HANDOFF.md` and `02_ROADMAP.md`
3. Then `web_fetch` whatever's relevant to the current task
4. At session end: update relevant docs, commit, push via VPS SSH
