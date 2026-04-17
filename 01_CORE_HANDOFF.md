# CORE SYSTEM HANDOFF v15

**Last updated:** April 15, 2026 ~7pm PT
**Owner:** Travis Gehring, Four Corners Repair Center (FCR)
**Legal entity:** Fish Are Friends LLC DBA Four Corners Repair Center
**Location:** 1455 N Milwaukee St, Boise ID 83704

## The Business

Travis owns FCR, a device repair shop doing $22-24k/mo revenue. Jane is an AI Chief of Staff system that handles automated tasks, reporting, and communication. The long-term vision: Jane runs autonomous revenue streams (LeadGate, LocalIQ, Etsy, Repair-Helper licensing) that scale beyond the shop.

**Team:**
- Travis Gehring — Owner
- Jonathan McKeeth — Store Manager
- Will Ross — Staff

**Jane's email:** jane@fourcornersrepair.com
**Travis's email:** travis@fourcornersrepair.com (observation mode for Gmail — Jane sends, Travis reads)

## Active VPS

- **IP:** 138.68.26.63 (fcr-openclaw, DigitalOcean, 8GB RAM, $48/mo)
- **OS:** Ubuntu 24.04
- **SSH:** `ssh root@138.68.26.63`
- **Env file:** `/root/fcr.env` — ALL credentials live here
  - MUST use `set -a; source /root/fcr.env; set +a` (has quoted values that need `-a` flag)
  - Fixed April 15: WP_APP_PASSWORD, DR_WP_APP_PASSWORD, UNSPLASH_ACCESS_KEY had unquoted spaces
- **Old server 209.38.136.50:** DESTROYED April 15

## Running Services

| Service | Port/Method | Status |
|---|---|---|
| OpenClaw (Jane core) | 18789 | Active |
| Repair-Helper Discord bot | Discord API | Active (PID varies) |
| LeadGate web app | 5050 | Active (outreach paused) |
| Claude Bridge v2 | Gmail polling (10s) | Active |
| Queue Flush | Cron */5 | Active |
| Self-Healer | Cron */30 | Active |

## Claude Bridge v2

The bridge lets Jane (OpenClaw) dispatch tasks to Claude for execution. Architecture:
- Jane creates a Gmail draft with subject `[CLAUDE-TASK]` containing bash commands
- Bridge polls every 10s, finds task drafts, executes commands
- Results written back as `[JANE-RESULT]` drafts
- WhatsApp alerts on failures via `/home/openclaw/.openclaw/workspace/whatsapp_queue.json`

**Key files:**
- `/root/jane-workers/claude_bridge.py.RETIRED (DISABLED)` — the bridge itself
- `/root/jane-workers/queue_flush.py` — WhatsApp-to-email bridge (cron */5)
- `/root/jane-workers/self_healer.py` — health monitoring (cron */30)

**Patches deployed April 15:**
- SSL retry: catches handshake timeouts, retries once after 2s sleep
- datetime.utcnow() → datetime.now(timezone.utc)
- queue_flush: derive_subject() for meaningful email subjects (was hardcoded "LeadGate Alert")
- self_healer: env path references updated from /root/shop-ai/.env → /root/fcr.env

**Backups:** Multiple timestamped .bak files in /root/jane-workers/

## Universal Customer Lifecycle (UCL) — Phase 0

Built April 15, 2026. This is the autonomous customer-handling engine.

**Directory structure:**
```
/root/jane-workers/
├── lifecycle/
│   ├── engine.py              (cron-driven main loop, filelock)
│   ├── signal_inject.py       (manual signal CLI)
│   ├── stages/
│   │   ├── signal.py          ✅ TESTED
│   │   ├── qualify.py         ✅ TESTED
│   │   ├── contract.py        ✅ TESTED (with manual NDA review)
│   │   ├── invoice.py         ⬜ NEXT
│   │   ├── payment_watch.py   ⬜
│   │   ├── provision.py       ⬜
│   │   ├── deliver.py         ⬜
│   │   ├── activate.py        ⬜
│   │   └── steward.py         ⬜
│   ├── lib/
│   │   ├── state.py           (customer state I/O)
│   │   ├── whatsapp.py        (alert queue)
│   │   ├── approval.py        (APPROVAL NEEDED email flow, supports attachments)
│   │   ├── gmail.py           (send + search + download attachments)
│   │   ├── nda_generator.py   (docx placeholder fill)
│   │   └── state_schema.json
│   └── templates/
│       ├── repair_helper_nda.docx
│       └── repair_helper_nda_email.txt
├── products/
│   └── repair_helper_license.json ($500 standard, $450-$550 auto-range)
├── customers/
│   └── {slug}/state.json, history.json, notes.md, artifacts/
└── deliverables/
    └── Repair-Helper-Prompt-Package.zip (26.8KB, md5: b85eb5a3a4b0eae5cfdb52a85d072173)
```

**Engine is NOT on cron yet.** Currently run manually via `python3 /root/jane-workers/lifecycle/engine.py`. Will add cron once all 9 stages are built + tested.

## Websites

- **fourcornersrepair.com** — main WordPress site, FCR Chat Widget v38 active (Stripe LIVE)
- **devicerescues.com** — Google Ads landing page ONLY (strict compliance: banned terms include repair, fix, broken, cracked)
- Widget deployed via `deploy_widget.py` (ships widget.js content via WordPress REST API)
- PHP plugin files on VPS are DEAD CODE — not deployed, don't touch

## Google Integrations

- **GBP:** OAuth authorized. Auto-post/auto-reply for 5-star reviews (no approval needed). Under 5-star: Jane drafts, Travis approves.
- **Google Ads:** API submitted April 4 (awaiting Basic access). MCC: 498-468-6699, FCR client: 827-380-6627.
- **GA4 + GSC:** Not yet connected (need Measurement IDs from Travis)

## Hard Rules (Claude must follow)

1. Root fixes only, never patches
2. One VPS terminal command at a time (Claude Code handles multi-step)
3. Google Ads compliance is sacred
4. Claude does the work, not Travis
5. Act as Chief of Staff — proactive, catch problems early
6. No docs/PDFs unless Travis explicitly asks — default to inline chat
7. MANUAL NDA SIGNATURE REVIEW is permanent (until DocuSign integration)
8. Bridge forbidden for financial/customer-facing/irreversible ops until Phase 0 proven
9. Claude must verify [JANE-RESULT] EXIT_CODE before next task
10. Multi-line scripts use fenced bash blocks, not multi-line CMD
11. `set -a; source /root/fcr.env; set +a` pattern required (env has quoted values)

## Anthropic API

- **Balance:** ~$40.72 as of April 15 (WATCH THIS)
- **Primary model:** claude-sonnet-4-6
- **Heartbeat model:** claude-haiku-4-5-20251001

## Stripe

- **Laura Moser:** cus_UKzEAkMlMHjC5v
  - Invoice 1: in_1TMJK3ImCdFEvPje9Ybiq09T — $175 PAID
  - Invoice 2: in_1TMJL0ImCdFEvPjeWAJW8qhX — $175 auto-bills April 28
- **Repair-Helper Product ID:** prod_UKzDKIBpggLiiU
- **Live key:** in /root/fcr.env as STRIPE_SECRET_KEY
- **Test key:** NEEDS TO BE ADDED (required for INVOICE stage development)


---

## RETIRED SYSTEMS — DO NOT RESTART

| System | File | How Killed | Replaced By |
|---|---|---|---|
| Discord Repair-Helper Bot | repair_bot.py.RETIRED | Process killed, systemd masked, file renamed | Repair-Helper V2 SaaS |
| Claude Bridge | claude_bridge.py.RETIRED | Process killed, file renamed | WhatsApp + Claude Code |

### Why These Were Retired
- **Discord bot:** Repair-Helper is now a SaaS product at $39.99/mo. Every shop gets their own login. The Discord bot gave it away for free.
- **Claude Bridge:** Jane communicates via WhatsApp. Claude Code handles VPS execution directly via SSH. The bridge (polling Gmail drafts) was redundant overhead.

### Self-Healer Updated
- `repair-helper` removed from self_healer.py service watch list
- `repair-helper` removed from repairdesk_worker.py service list
- `repair-helper` removed from dashboard_updater.py status check
- `clawdbot-repair` restart call neutralized
- Self-healer now monitors: `openclaw`, `leadgate` only

### Caddy Proxy Change (April 17, 2026)
- **Old:** tryleadgate.com → reverse_proxy 127.0.0.1:18789 (openclaw gateway)
- **New:** tryleadgate.com → reverse_proxy 127.0.0.1:5050 (Flask app directly)
- **Reason:** openclaw gateway was caching/serving stale HTML, hiding all V2 dashboard features
- **WARNING:** If Caddy config reverts to 18789, dashboard features will appear missing
