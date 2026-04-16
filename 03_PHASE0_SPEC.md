# Phase 0 — Universal Customer Lifecycle (UCL) Spec

**Last updated:** April 16, 2026 ~3am UTC
**Status:** 7 of 9 stages built + tested. STEWARD is last.

## Build Status

| Stage | Status | Key behavior |
|---|---|---|
| SIGNAL | TESTED | Validates name + email, creates state file, WhatsApp alert |
| QUALIFY | TESTED | Price range, payment schedule, email format. Auto-approve or Travis approval |
| CONTRACT | TESTED | NDA gen, email, poll for signed return, forward to Travis for manual review |
| INVOICE | TESTED | Stripe customer + invoice. Test/live key routing. Draft-first then add item then finalize |
| PAYMENT_WATCH | TESTED | Poll Stripe for paid status. Uses attribute access on Stripe objects |
| PROVISION | SKIPPED | Not needed for Repair-Helper |
| DELIVER | TESTED | Email zip. Draft-first-3 gating. MD5 verification |
| ACTIVATE | TESTED | Day-7 check-in (reviewed), day-30 referral (auto after first approval) |
| STEWARD | NEXT | Subscription health, ongoing monitoring |

## Bugs found and fixed

1. INVOICE: InvoiceItem not attaching. Fix: create Invoice as draft, add InvoiceItem with explicit invoice param, finalize
2. PAYMENT_WATCH: Stripe objects use attribute access (invoice.status) not dict access (invoice.get("status"))
3. payment_watch.py lost during heredoc paste. Re-created via SCP
4. deliver.py template didn't transfer on SCP. Re-created via heredoc
5. /root/fcr.env had unquoted spaces in WP_APP_PASSWORD, DR_WP_APP_PASSWORD, UNSPLASH_ACCESS_KEY

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
- /root/jane-workers/deliverables/Repair-Helper-Prompt-Package.zip (26.8KB, md5: b85eb5a3a4b0eae5cfdb52a85d072173)

## Test results (April 15-16)

All tests used Stripe TEST mode. No real money touched.

| Test | Result |
|---|---|
| Happy path SIGNAL to QUALIFY | PASSED - 2 ticks, 160ms |
| Price out of range (approval) | PASSED - Real Gmail sent |
| Missing email (hard fail) | PASSED - Terminated cleanly |
| CONTRACT NDA gen + send | PASSED - Real NDA emailed to travisgehring@me.com |
| CONTRACT NDA return detection | PASSED - PDF found + downloaded |
| CONTRACT manual review | PASSED - PDF forwarded to Travis, APPROVED processed |
| INVOICE Stripe create | PASSED - $500 invoice (TEST MODE) |
| INVOICE bug fix (empty invoice) | PASSED - Re-tested, $500 correct |
| PAYMENT_WATCH detect paid | PASSED - Transitioned to DELIVER |
| DELIVER draft-first-3 gate | PASSED - Blocked for review |
| DELIVER send zip | PASSED - Real zip emailed |
| ACTIVATE day-7 (draft reviewed) | PASSED - Sent after APPROVED |
| ACTIVATE day-30 (auto) | PASSED - Auto-sent (trust earned from day-7 approval) |

## Stripe test objects

- Test customer: cus_ULMIVQzyty9vEA
- Test invoice (voided, was empty): in_1TMfZfIP2f7YVDWThAnJvH21
- Test invoice (correct, paid): in_1TMfceIP2f7YVDWTXvvbXRS6

## Remaining for Phase 0 complete

1. Build STEWARD stage (lightweight)
2. Add engine to cron (every 2 min)
3. Clean up test customer, inject 3 fresh test customers
4. Let cron process all 3 end-to-end without manual intervention
5. Declare Phase 0 proven
