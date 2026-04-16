# Laura Moser Deal Record

**Status:** CLOSED + DELIVERED
**Closed:** April 15, 2026

## Deal summary

- **Customer:** Laura Moser, Geek to Go (MN)
- **Email:** laura@geek2gomn.com
- **Product:** Repair-Helper Prompt Package License
- **Price:** $350 (friend pricing — standard is $500)
- **Payment:** 2 x $175, 14 days apart
- **NDA:** Signed April 14, 2026

## Stripe

- **Customer ID:** cus_UKzEAkMlMHjC5v
- **Invoice 1:** in_1TMJK3ImCdFEvPje9Ybiq09T — $175 PAID April 15
- **Invoice 2:** in_1TMJL0ImCdFEvPjeWAJW8qhX — $175 auto-bills April 28 (auto_advance=True)
- **Deleted duplicate customer:** cus_UKnzYxWOFhngOE (polluted by Jane's bridge failure)

## What was delivered

**Package:** Repair-Helper-Prompt-Package.zip (~27 KB, 8 files)

Included (over-delivered for referral upside):
- README.md
- 1-System-Prompt.md (actual production prompt, sanitized)
- 2-Architecture-and-Escalation.md
- 3-Discord-Setup-Guide.md
- 4-Bot-Reference-Implementation.py (sanitized Python bot)
- 5-Customization-Guide.md
- 6-Quick-Start.md
- env.example

Bot named "Gideon" per Laura's preference. Sanitized clean — no FCR paths, no API keys, no Boise references, no Jane references.

## Post-delivery

- Laura asked about Node.js vs Python (told her stay Node), Railway vs VPS (Railway fine, add volume), WordPress SOPs (optional). Reply sent April 15.
- Laura has promised referrals. Friends pricing was deliberate investment in word-of-mouth.
- Day-7 check-in due ~April 22
- Day-30 referral ask due ~May 15

## Action items

- ⬜ **April 28:** Verify Stripe auto-sent Invoice 2 and it cleared
- ⬜ **~April 22:** Friendly check-in email ("how's Gideon coming along?")

## Why this deal matters for Phase 0

Laura's deal was done 100% manually via SSH because Jane's bridge failed silently for 14 hours. Every failure mode from Laura's deal is now encoded into the UCL design:
- Incorrect product staging → QUALIFY stage validates product + price
- Silent bridge failure → WhatsApp alerts on every stage transition
- Wrong Stripe customer → idempotent customer creation with slug-based lookup
- Manual approval → built into every critical stage
