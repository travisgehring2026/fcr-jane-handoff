# Social Media Integration Spec — Build Our Own

**Created:** April 16, 2026
**Status:** PLANNED — Phase 2 workstream
**Decision:** Build our own integrations. No Composio. No third-party fees. Full IP ownership.

## Why build our own

- Facebook/Instagram/TikTok APIs are free to use
- Composio would charge $29/mo (growing with scale) as a middleman
- Building our own means the integration code is our IP
- Directly reusable for LocalIQ client social media management
- Same architectural pattern as lib/gmail.py (proven tonight)
- Room to grow is unlimited — no tier caps, no dependency on third-party pricing

## Architecture

Same library pattern as the rest of Jane's integrations:

```
/root/jane-workers/lifecycle/lib/
├── instagram.py       Meta Graph API — photos, stories, reels, insights
├── facebook.py        Meta Graph API — page posts, comments, review shares
└── tiktok.py          TikTok API — video uploads, content management, analytics
```

Plus a content scheduling system:

```
/root/jane-workers/lifecycle/
├── social/
│   ├── content_scheduler.py     Cron worker that generates + schedules posts
│   ├── content_templates/       Per-platform content templates
│   └── media/                   Staging area for images/videos before posting
```

## Platform details

### Instagram (via Meta Graph API)

API: https://developers.facebook.com/docs/instagram-platform/instagram-graph-api
Auth: Facebook App + Instagram Business Account linked to Facebook Page
Token: Long-lived token (60 days), auto-refresh via our OAuth pattern
Capabilities:
- Post photos with captions
- Post carousel (multiple images)
- Post stories
- Post reels (short video)
- Get engagement insights (likes, comments, reach, impressions)
- Reply to comments
- Get follower metrics

Content types for FCR:
- Before/after repair photos (highest engagement for repair shops)
- Tech tip graphics (Claude generates, Canva API or template renders)
- Customer testimonial quotes
- "What's inside your [device]" educational posts
- Staff spotlights
- Seasonal promotions

### Facebook (via Meta Graph API — same API as Instagram)

API: https://developers.facebook.com/docs/pages-api
Auth: Same Facebook App as Instagram (one OAuth, both platforms)
Capabilities:
- Post to FCR's Facebook Page (text, photos, links, videos)
- Respond to page comments
- Share Google Reviews as posts
- Create events
- Post to Facebook Marketplace (refurbished devices — future revenue stream)
- Get page insights

Content types for FCR:
- Local community engagement posts
- Review reshares ("Another happy customer!")
- "Did you know?" tech education
- Repair service announcements
- Staff introductions
- Local Boise event tie-ins

### TikTok (separate API from Meta)

API: https://developers.tiktok.com/doc/content-posting-api-get-started
Auth: TikTok for Business account + API credentials
Capabilities:
- Upload videos (up to 10 min)
- Query video performance metrics
- Manage creator profile
- Get audience insights

Content types for FCR:
- 30-second repair teardown clips
- "What went wrong" diagnostic walkthroughs
- Satisfying screen replacement videos
- "Day in the life at a repair shop"
- Trending audio + repair footage mashups
- Customer reaction videos (with permission)

## Content pipeline workflow

1. GENERATE: Jane identifies content opportunities
   - New 5-star review -> Facebook/Instagram testimonial post
   - Interesting repair completed -> before/after Instagram post + TikTok clip
   - Tech news (new iPhone release, etc.) -> educational post across all platforms
   - Weekly schedule: Mon/Wed/Fri posts minimum

2. DRAFT: Jane creates the post
   - Selects platform(s)
   - Generates caption/copy
   - Selects or requests media (photo/video)
   - Applies platform-specific formatting (hashtags for Instagram, music for TikTok)

3. APPROVE: Travis reviews (uses same approval workflow as NDA/delivery)
   - Email: "APPROVAL NEEDED: Instagram post — before/after MacBook repair"
   - Body: caption text, image preview link, scheduled time
   - Travis replies APPROVED or REJECTED (with notes)
   - Draft-first-N gating: first 3 weeks require approval, then autonomous

4. PUBLISH: Jane posts at optimal time
   - Instagram: 11am-1pm and 7-9pm local time (Boise = Mountain)
   - Facebook: 9am-12pm weekdays
   - TikTok: 6-10pm any day
   - Cross-post where appropriate (same image, different caption/format)

5. MONITOR: Jane tracks performance
   - Engagement metrics (likes, comments, shares, views)
   - Follower growth
   - Weekly summary report to Travis
   - Flag any negative comments for Travis to handle

## Prerequisites (Travis must do before we build)

1. Facebook Business Page for FCR (may already exist)
2. Instagram Business account linked to that Facebook Page
3. TikTok Business account for FCR
4. Meta Developer App (developers.facebook.com) — register as developer, create app
5. TikTok Developer App (developers.tiktok.com) — register, create app
6. API credentials stored in /root/fcr.env:
   - META_APP_ID, META_APP_SECRET, META_PAGE_ACCESS_TOKEN
   - TIKTOK_CLIENT_KEY, TIKTOK_CLIENT_SECRET, TIKTOK_ACCESS_TOKEN
   - INSTAGRAM_BUSINESS_ACCOUNT_ID

## Build estimate

| Component | Time | Session |
|---|---|---|
| lib/instagram.py (Meta Graph API) | 2 hours | Session 1 |
| lib/facebook.py (Meta Graph API, shared auth) | 1-2 hours | Session 1 |
| lib/tiktok.py (TikTok API) | 2-3 hours | Session 2 |
| Content scheduler + approval workflow | 2 hours | Session 2 |
| Testing + first real posts | 1-2 hours | Session 3 |
| Total | 8-10 hours | 2-3 sessions |

## LocalIQ reuse plan

Once proven on FCR's own accounts:
- Add social_media_management product config to /products/
- Each LocalIQ client gets their own set of API credentials in their customer state
- Jane manages posting, engagement, and reporting per-client
- Pricing: included in LocalIQ monthly ($49.99) or as add-on ($29.99/mo)
- Cost to serve per client: $0 in platform fees, only Anthropic API cost for content generation
- Margin: ~90%+ on social media management

## Relevant Composio skills for reference (not using, but architecturally useful)

These skill docs from the awesome-claude-skills repo are useful as API reference even though we are not using Composio itself:
- instagram-automation: documents the Meta Graph API endpoints and patterns
- facebook-automation: same API, page-specific endpoints
- tiktok-automation: TikTok Content Posting API patterns

The skill SKILL.md files contain the API endpoint lists, parameter schemas, and known pitfalls that save us research time during build.
