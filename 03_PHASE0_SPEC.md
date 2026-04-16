# Phase 0 — Universal Customer Lifecycle (UCL) Spec

**Last updated:** April 16, 2026 ~4pm PT
**Status:** ALL 9 STAGES BUILT AND TESTED. Awaiting cron setup + 3 end-to-end tests.

## Build Status

| Stage | Status | Key behavior |
|---|---|---|
| SIGNAL | TESTED | Validates name + email, creates state file, WhatsApp alert |
| QUALIFY | TESTED | Price range check, auto-approve or Travis approval |
| CONTRACT | TESTED | NDA gen, email, poll for signed return, manual review by Travis |
| INVOICE | TESTED | Stripe customer + invoice, test/live key routing, draft-first creation |
| PAYMENT_WATCH | TESTED | Poll Stripe for paid status, attribute access on Stripe objects |
| PROVISION | SKIPPED | Not needed for Repair-Helper |
| DELIVER | TESTED | Email zip, draft-first-3 gating, MD5 verification |
| ACTIVATE | TESTED | Day-7 check-in (reviewed), day-30 referral (auto after first approval) |
| STEWARD | TESTED | All 7 features: payment monitoring, subscription health, inbox monitoring, referral detection, weekly reports, churn handling, renewal opportunities |

## Bugs found and fixed

1. INVOICE: InvoiceItem not attaching. Fix: create Invoice as draft, add InvoiceItem with explicit invoice param, finalize
2. PAYMENT_WATCH: Stripe objects use attribute access (invoice.status) not dict access (.get())
3. payment_watch.py lost during heredoc paste. Re-created via SCP
4. deliver.py template didn't transfer on SCP. Re-created via heredoc
5. /root/fcr.env had unquoted spaces in WP_APP_PASSWORD, DR_WP_APP_PASSWORD, UNSPLASH_ACCESS_KEY
6. Bridge crash loop from stale [CLAUDE-TASK] restart draft. Fixed by deleting draft + restarting.

## Files on VPS

Lifecycle engine and stages:
- /root/jane-workers/lifecycle/engine.py
- /root/jane-workers/lifecycle/signal_inject.py
- /root/jane-workers/lifecycle/stages/signal.py
- /root/jane-workers/lifecycle/stages/qualify.py
- /root/jane-workers/lifecycle/stages/contract.py
- /root/jane-workers/lifecycle/stages/invoice.py
- /root/jane-workers/lifecycle/stages/payment_watch.py
- /root/jane-workers/lifecycle/stages/deliver.py
- /root/jane-workers/lifecycle/stages/activate.py
- /root/jane-workers/lifecycle/stages/steward.py (779 lines, 7 features)

Libraries:
- /root/jane-workers/lifecycle/lib/state.py
- /root/jane-workers/lifecycle/lib/whatsapp.py
- /root/jane-workers/lifecycle/lib/approval.py (supports attachments)
- /root/jane-workers/lifecycle/lib/gmail.py
- /root/jane-workers/lifecycle/lib/nda_generator.py
- /root/jane-workers/lifecycle/lib/state_schema.json

Templates:
- /root/jane-workers/lifecycle/templates/repair_helper_nda.docx
- /root/jane-workers/lifecycle/templates/repair_helper_nda_email.txt
- /root/jane-workers/lifecycle/templates/repair_helper_delivery.txt

Product config:
- /root/jane-workers/products/repair_helper_license.json

Deliverable:
- /root/jane-workers/deliverables/Repair-Helper-Prompt-Package.zip (26.8KB)

## Test results (April 15-16)

All tests used Stripe TEST mode. No real money touched.

| Test | Result |
|---|---|
| Happy path SIGNAL to QUALIFY | PASSED |
| Price out of range (approval) | PASSED |
| Missing email (hard fail) | PASSED |
| CONTRACT NDA gen + send | PASSED |
| CONTRACT NDA return detection | PASSED |
| CONTRACT manual review | PASSED |
| INVOICE Stripe create ($500 TEST) | PASSED |
| INVOICE bug fix (empty invoice) | PASSED |
| PAYMENT_WATCH detect paid | PASSED |
| DELIVER draft-first-3 gate | PASSED |
| DELIVER send zip | PASSED |
| ACTIVATE day-7 (draft reviewed) | PASSED |
| ACTIVATE day-30 (auto) | PASSED |
| STEWARD all 7 features | PASSED |

## Stripe test objects

- Test customer: cus_ULMIVQzyty9vEA
- Test invoice (paid): in_1TMfceIP2f7YVDWTXvvbXRS6

## Remaining for Phase 0 PROVEN

1. Add engine to cron (every 2 min)
2. Clean up test customer, inject 3 fresh test customers
3. Let cron process all 3 end-to-end without manual intervention
4. Declare Phase 0 proven
