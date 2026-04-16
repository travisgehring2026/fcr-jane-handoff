# Peace of Mind Plan — Full Database Email Campaign Spec

**Created:** April 16, 2026
**Status:** PLANNED — Quick Win, independent of phase order
**Priority:** HIGH — do next week. Years of untapped PC repair customers.

## Background

Jane currently sends Peace of Mind Plan emails to customers from the last 90 days only. Travis identified that the FULL RepairDesk database has years of PC repair customers who have never heard about POM. This is revenue sitting on the table.

## Campaign rules

1. ONE TIME SEND ONLY — each customer gets this email exactly once, ever
2. PC REPAIRS ONLY — filter by repair type, exclude phone/tablet/console-only customers
3. AUTHORIZED EMAIL ONLY — customer must have email on file AND not opted out
4. EXCLUDE BUSINESS ACCOUNTS — business customers get different treatment
5. SUPPRESS AFTER SEND — maintain a suppression list, never re-send to anyone who received it
6. ATTACH POM FLYER — PDF attachment with the Peace of Mind Plan details

## Email content direction (Travis's brief)

- Straightforward but whimsy and eye-catching
- Why POM is better than Norton, McAfee, etc.
- Emphasize: you already know us, we are your local repair shop
- What the Peace of Mind Plan includes
- Pricing: Basic $149.99/yr, Plus $249.99/yr
- Not salesy-aggressive — informative and genuine
- Tone: "hey, we built something we think you'd actually want"

## Key messaging points

Why POM beats Norton/McAfee:
- Norton/McAfee are faceless corporations that sell fear. We are real people who have already fixed your computer.
- Antivirus software alone does not prevent the problems we see every day: slow performance, malware, misconfigured settings, failing hardware
- POM is PROACTIVE — we check on your computer before it breaks, not after
- If something does go wrong, you are already covered — no diagnostic fees, priority service
- We know YOUR computer because we have worked on it before
- Local support means a real person picks up the phone, not a chatbot in another country

What POM includes (Basic $149.99/yr):
- Annual diagnostic tune-up
- Priority scheduling
- 10% off all repairs
- Remote support included
- Malware removal included

What POM Plus includes ($249.99/yr):
- Everything in Basic
- Quarterly tune-ups (4x/year)
- Free virus/malware removal
- Hardware diagnostics included
- Extended warranty on repairs

## Technical implementation

### Step 1: Extract the list from RepairDesk

Use RepairDesk API to pull:
- All customers with at least one PC/laptop/desktop repair
- Filter: has email address, email marketing authorized (opt-in), not a business account
- Fields needed: name, email, last repair date, device type, total repairs count

API endpoint: /api/v1/customers or /api/v1/invoices (whichever gives repair type filtering)
Note: RepairDesk API may require pagination at 100 results per page

### Step 2: Deduplicate and clean

- Remove duplicate emails (same email, multiple repair tickets)
- Remove any emails already in the 90-day POM send list (they already got a version)
- Remove any emails in the existing suppression list
- Validate email format (basic regex)
- Result: clean list with name + email + last repair date

### Step 3: Prepare the email

- HTML email with inline styling (no external CSS — email clients strip it)
- POM flyer attached as PDF
- Personalization: customer first name, approximate last visit ("we last worked on your computer back in [month/year]")
- Unsubscribe link at bottom (CAN-SPAM compliance)
- From: jane@fourcornersrepair.com (or travis@)
- Reply-to: travis@fourcornersrepair.com

### Step 4: Send in batches

Gmail Workspace daily limit: ~2,000 emails/day
Recommended batch size: 200-300/day (stay well under limit, avoid spam flags)
Schedule: send batches at 10am Mountain time (best open rates for service emails)

Implementation:
- Cron job runs daily at 10am MT
- Reads the unsent portion of the clean list
- Sends next batch (200-300)
- Records each send in suppression file
- Stops when list is exhausted

### Step 5: Track and suppress

Suppression file: /root/jane-workers/campaigns/pom_blast_2026/sent.json
Format: {"email": "...", "sent_at": "...", "message_id": "..."}
This file is checked before every future POM-related send to prevent doubles

### Step 6: Monitor

- Daily WhatsApp update to Travis: "POM Campaign: 287/2,341 sent today. 1,453 remaining."
- Track bounce-backs (Jane monitors inbox for delivery failures)
- If bounce rate exceeds 5%: pause and alert Travis (list quality issue)

## Risks and mitigations

| Risk | Mitigation |
|---|---|
| Gmail rate limiting | Send 200-300/day, well under 2,000 limit |
| Spam flagging | Genuine content, real reply-to, unsubscribe link, existing customer relationship |
| Bad email addresses (bounces) | Monitor bounce rate, pause at 5% |
| Customer complaints | Unsubscribe link, genuine tone, one-time only |
| RepairDesk API limitations | Paginate properly, cache results locally |

## CAN-SPAM compliance checklist

- Accurate "From" header (jane@ or travis@fourcornersrepair.com)
- Non-deceptive subject line
- Physical address included (1455 N Milwaukee St, Boise ID 83704)
- Unsubscribe mechanism (reply "unsubscribe" or click link)
- Honor opt-outs within 10 business days
- Identified as an advertisement (subtle but present)

## Build estimate

| Task | Time |
|---|---|
| RepairDesk API list extraction | 1-2 hours |
| Email HTML template + copy | 1 hour (Claude drafts, Travis approves) |
| Batch sender script | 1 hour |
| Suppression tracking | 30 min |
| Testing (send to Travis first) | 30 min |
| Total | 4-5 hours (1 session) |

## Success metrics

- Open rate target: 25-35% (existing customer relationship = high trust)
- Click rate target: 5-10%
- Conversion target: 2-5% of opens → POM signup inquiry
- If 2,000 customers receive it and 2% convert: 40 new POM subscribers
- 40 × $149.99/yr = $6,000/yr in new recurring revenue from ONE email
