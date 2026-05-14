# Product Requirements Document (PRD)

_Fill this out by having a conversation with Claude. Don't try to write it alone on a blank page — that's what the AI is for. Use the `/skills/pm-interview` skill to drive the conversation._

> **Status:** Draft (sections 1–8 complete; ready for review)
> **Last updated:** 2026-05-13
> **Author:** Ernesto Ramos
> **Stakeholder:** Residents of Windsor, CO (a small town, pop. ~37k)

---

## 1. The problem

People in Windsor, CO regularly miss out on local events — tech meetups, bar tournaments, community gatherings — because the information is **fragmented and noisy**. Today, would-be attendees check Meetup.com (too broad — pulls in events from all of northern Colorado, not just Windsor), the Windsor community Facebook group and NextDoor (drowned in non-event posts), the city events page, and physical flyers at coffee shops and bars. Some events (like the Mario Kart tournament at the local bar) live *only* on a paper flyer and never show up online at all. The pain is moderate — a 6/10, "would-have-been-nice-to-know" rather than "I'd pay to fix this" — so the bar for adoption is high: a new tool only wins if it's dramatically less effort than the status quo.

**Concrete examples that motivated this:**
- Matt missed a C# meetup advertised on Meetup.com because the signal was buried in unrelated regional events.
- Joe (and the author) missed a Mario Kart tournament at a local Windsor bar — the only ad was a flyer inside the bar.

---

## 2. The user

- **Primary user:** A 35–55-year-old adult who lives in Windsor, CO, works a typical 9-to-5 job, and likely has kids at home. They're looking for "something to do this weekend" and want a mix of family-friendly events *and* adult events (date nights, meetups, bar events). Real-world stand-ins: the author's friends Matt and Joe.
- **Their current workflow:** Today they piece together a picture of "what's going on" by checking Meetup.com (too broad — pulls in regional events outside Windsor), the Windsor Facebook group and NextDoor (too noisy — events drown in non-event posts), the city events page, and physical flyers seen in passing. Many bar/restaurant events never appear online at all. They often find out about events after they've already happened.
- **Trigger / moment of use:** Thursday or Friday evening, on the couch, phone in hand: *"I want to do something this weekend — what's going on?"* They'd open the app cold (no push notifications in v1) and want to be able to filter by event type (family-friendly vs. adult, food/music/sports/tech, etc.) to narrow the list quickly.
- **Their technical comfort:** Comfortable with a mobile browser, Facebook, banking apps. **Will not install** a native app for an unproven product. Bookmarking a URL is the install ceiling.
- **Device:** Phone, mobile web. Desktop is a nice-to-have, not a target.

---

## 3. What success looks like

- **Must-have outcome (the bar):** Within 3 months of launch, **at least 50 unique users have marked interest in (or RSVP'd to) at least one event** on the site. This is the "first sign of life" demand metric — proves people care enough to express intent, not just browse.
- **The one feature that has to be excellent:** the **discovery feed**. If the feed feels good and is full of real Windsor events, the product works. If the feed is empty, stale, or feels generic, nothing else matters. Filtering is important but secondary — a great unfiltered feed beats a bad filtered one.
- **Kill criterion:** if not enough events get posted to sustain a useful feed. Concretely, this means the supply side has failed: the invited curators (school admins, Chamber, 3 small businesses) and the author's manual flyer-scraping aren't producing enough content. Without a steady event stream, there's no feed and no product.
- **Not a goal in v1:** monetization (this is a community service, not a business), open public event posting, moderation tooling, native mobile apps, push notifications, ticketing/payments, attendance tracking after the event.

---

## 4. Core user stories

There are two user types: **Browsers** (Windsor residents looking for things to do) and **Posters** (invited curators + the author).

### Must

1. **[Must — Browser]** As a Windsor resident, I want to see a list of upcoming local events on my phone, so I can find something to do this weekend.
2. **[Must — Browser]** As a Windsor resident, I want to see event details (where, when, cost, family-friendly?), so I can decide whether to go.
3. **[Must — Browser]** As a Windsor resident, I want to mark an event as "interested," so I remember it later — and so the site can count interest toward the success metric.
4. **[Must — Poster]** As an invited curator, I want to log in, so I can post events on behalf of my org.
5. **[Must — Poster]** As an invited curator, I want to create a new event with title, date/time, location, description, type, and family-friendly flag, so it appears in the feed.
6. **[Must — Poster]** As an invited curator, I want to edit or delete events I created, so I can correct mistakes or cancel.

### Should

7. **[Should — Browser]** As a Windsor resident, I want to filter the feed by event type (family-friendly vs. adult; food/music/sports/tech/…), so I can narrow the list quickly. *Important but not essential — a great unfiltered feed still delivers value.*
8. **[Should — Browser]** As a Windsor resident, I want to see only events happening this weekend (or in the next 7 days), so I don't have to scroll through far-off dates.

### Could

9. **[Could — Browser]** As a Windsor resident, I want to share an event with a friend via SMS / a copyable link, so we can decide together.
10. **[Could — Poster]** As a curator, I want to see how many people marked my event "interested," so I know what's resonating.

### Won't (this release)

- Public/open event posting (v2 — needs moderation tooling first).
- Ratings, reviews, or comments.
- RSVPs with capacity limits or ticketing/payments.
- Push notifications (v2 once we have the audience).
- Calendar (Google/Apple) integration.
- Native mobile apps.

---

## 5. Out of scope

The point of this section is to write down the things the author has *already been tempted by* and is consciously choosing not to build in v1. Everything below is "v2-or-never," not "we forgot."

- **Browser account creation.** Browsers stay anonymous in v1, identified only by a cookie-stored anonymous ID. "I'm interested" is tracked against that anonymous ID. No signup, no login, no password reset, no account-merge logic. *Why out:* the success metric (50 unique users mark interest) works with anonymous IDs alone; account creation costs real surface area without moving that needle. Pushed to v2 once there's a real audience asking for cross-device saved lists.
- **Email digests** ("here's what's happening in Windsor this weekend"). *Why out:* email is a noisy channel; the author believes most users will ignore it. The "open cold" trigger risk stays — accepting this consciously.
- **Push notifications.** *Why out:* requires native app or PWA install, and the audience won't install. v2 candidate once we have an engaged user base and a way to deliver them.
- **SMS notifications.** *Why out:* per-message cost, opt-in/STOP compliance overhead, real privacy implications. v2-or-never.
- **Ticketing / payments.** *Why out:* not a goal; this is a community service, not a marketplace.
- **Public, open event posting.** Posting is invite-only in v1 (school admins, Chamber, 3 small businesses, the author). *Why out:* opening it up requires moderation tooling, abuse handling, and a content-policy story we don't have yet. Pushed to v2.
- **Moderation tooling** (report, hide, ban, content review queues). *Why out:* unnecessary while posting is invite-only and trusted.
- **Ratings, reviews, comments** on events. *Why out:* social features attract abuse and require moderation; not needed for the discovery feed to work.
- **Map / geo view.** *Why out:* Windsor is small (~37k people, ~5 sq mi developed area). A list with a location field is enough; a map is polish.
- **Native mobile apps.** Mobile web only. *Why out:* the audience won't install, and a mobile-friendly responsive site reaches them with zero friction.
- **Calendar integration** (Google/Apple add-to-calendar). *Why out:* nice-to-have, not on the critical path. Could land as a "Could" later if cheap.
- **Scraping / auto-import** from Meetup, Facebook events, the city events page. *Why out:* fragile, ToS-risky, and unnecessary at v1 scale — manual posting + the author filling in flyers covers initial supply.
- **Attendance tracking** after the event ("did you go? how was it?"). *Why out:* not on the path to the success metric.

---

## 6. Technical shape

- **Type of app:** Full-stack mobile-friendly responsive web app. One Worker serves both static assets (the React/HTML UI) and the JSON API. No native app, no PWA install prompt in v1.
- **Stores data:** yes — both structured records (events, curators, "interested" rows tying anonymous IDs to event IDs) and files (one image per event).
- **Authentication:** yes, but **only for curators** (~5–10 trusted invited posters: school admins, Chamber, 3 small businesses, the author himself). Curators use email + password. **Browsers do not authenticate** — they get an anonymous ID stored in a cookie, used to record "interested" clicks.
- **External services:** none in v1. No email sending, no SMS, no payments, no scraping, no third-party event imports, no AI. (This is a deliberate scope choice — every external dependency is a v1 cost we're not paying.)
- **Who pays for hosting:** the author. The whole stack is expected to fit comfortably inside Cloudflare's free tier at Windsor scale (a few thousand monthly visits, a few hundred events/year, low-tens of MB of images). If we exceed free tier, that's a *good* problem.

### Proposed Cloudflare stack

| Need | CF Product | Why |
|---|---|---|
| Hosting the web UI + API | **Workers** with **Static Assets** | One deploy target for both the frontend bundle and the API. Simpler than splitting Pages + Workers; same domain, no CORS, single `wrangler deploy`. |
| Structured data (events, curators, interests) | **D1** | SQLite-backed, serverless, generous free tier. Event volume is tiny (hundreds/year), reads are higher (browser views) but still fits. |
| Image storage (one image per event) | **R2** | Object storage with no egress fees. Cheap, simple, sufficient — we don't need on-the-fly transforms in v1, so plain R2 beats paid Cloudflare Images. We'll resize client-side or accept reasonable upload sizes. |
| Sessions for curator auth | **D1** (session table) or **signed cookies** | Skip KV in v1. A `sessions` row in D1 keyed by a random token, plus an HTTP-only cookie, is enough for ~10 curators. Reassess only if it gets chatty. |
| AI features | **None in v1** | No use case. *(Possible v2: auto-categorize events from the description.)* |
| Email | **None in v1** | No password reset emails, no digests, no magic links. Curators contact the author if locked out. |

**Why no KV:** for sessions at this scale, D1 is fine and avoids a second binding to reason about. KV becomes interesting only if we add hot-path read caching, which is a v2 problem.

**Why no Cloudflare Images:** it's a paid product solving a problem we don't have yet (responsive variants, on-the-fly transforms). R2 + a sensible upload size cap is enough for v1.

**Framework:** Hono for the API + a minimal React frontend (or vanilla TS — to be decided in `docs/DESIGN.md`). Vitest with `@cloudflare/vitest-pool-workers` for tests, per the project's CLAUDE.md.

---

## 7. Risks and unknowns

- **Biggest risk — supply.** Supply is upstream of every other risk. If invited curators (school admins, Chamber, the 3 small businesses) and the author's manual flyer-scraping don't produce a steady event stream, the feed is empty and nothing else matters — demand, discovery, and curator UX are problems you only *get to have* once supply works. This is the same condition as the §3 kill criterion, but called out here as the single biggest project risk, not just a tripwire.

- **Things I don't know how to do yet — LLM answer-engine optimization (AEO).** The CF/auth/D1/R2 mechanics are familiar; Claude can fill any small gaps. The real unknown is making sure that when a Windsor resident asks an LLM (ChatGPT, Perplexity, etc.) *"what's going on in Windsor this weekend?"*, this site shows up as a cited source with a working link back to the event page. This overlaps with classic Google SEO but isn't the same problem — and isn't something the author does day-to-day. Research task for week 1–2: figure out what structured data, sitemaps, robots/llms.txt, server-rendering, and content patterns are actually known to work for AEO in 2026.

- **Things I'm assuming but haven't verified:**
  - **Demographic.** The PRD targets 35–55-year-olds with kids based on the author's friend group (Matt, Joe), not on broader Windsor data. The earlier hypothesis was 16–25-year-olds; that shifted, but neither version has been validated with anyone outside the author's circle. If the real audience is, say, retirees or college-age, the event mix and tone could be wrong.
  - **"The audience won't install a native app."** This is asserted in §5 as a reason to stay mobile-web-only, but it's an assumption, not a verified fact. It's plausibly wrong for the "wants reminders for things I marked interested" use case — the most engaged users may well install something if it saved them from missing events. v1 ships as mobile web regardless; the assumption gets retested if/when v2 push notifications come up.

---

## 8. Milestones

- **Week 2 end — Read-only feed deployed.** Public Worker URL is live. The feed renders a list of upcoming events seeded from ~5–10 real Windsor events the author has entered by hand. Event detail pages exist. No "interested," no posting, no auth yet. Mobile-friendly. SEO + AEO foundation lands here (server-rendered HTML, sitemap, structured data, `llms.txt`) rather than getting retrofitted — it's harder to bolt on later.
  - **Covers:** Must stories 1, 2.

- **Week 3 end — Full loop closed end-to-end.** Browsers can click "I'm interested" against an anonymous-ID cookie, and the click counts toward the success metric. Curator auth (email + password, D1-backed sessions) is wired. At least one curator other than the author has logged in and posted a real event. Edit/delete works.
  - **Covers:** Must stories 3, 4, 5, 6.

- **Week 4 demo — Polish + supply test.** Mobile UX tightened. Filtering (Should story 7) lands if time permits. **At least 3 invited curators have actually posted ≥1 event each** — this is the supply assumption being tested live, not just theoretically. Site is indexed by Google and surfaces in at least one LLM answer to *"what's going on in Windsor this weekend?"*. Demo walkthrough: open the site on a phone, scroll the feed, filter to family-friendly, mark one event interested, then log in as a curator and post a new one.
