# Build Plan

_This file is the phased build plan for the project. It's the bridge between `docs/PRD.md` (what to build) + `docs/DESIGN.md` (what it looks like) and the actual code. Fill it out with the `build-plan` skill after the PRD and design brief are stable. Re-run the skill whenever reality has diverged from the plan._

> **Status:** Draft (phases being filled in)
> **Last updated:** 2026-05-13
> **Current phase:** Phase 0 (not yet started)

---

## Why a build plan exists

Claude Code sessions have a finite context window. The cheaper a session is to start, the better the work tends to be. A good build plan slices the project into phases where each phase:

- Has a single user-visible outcome.
- Touches a bounded set of files.
- Names exactly which docs and files Claude should load to execute it.
- Leaves the repo in a clean, testable state at the end.

That way each phase fits in a focused session — no full-repo loads, no thrashing, no context exhaustion mid-implementation.

---

## Strategy

- **Slicing principle:** Vertical slices by user-visible outcome. Each phase ships one slice end-to-end — DB schema → API handler → UI — so a deployed URL gets more useful with every phase. Horizontal (data layer first) was considered and rejected: the data model is straightforward (events, curators, interests, sessions) and "schema-first" would delay the first deployable feed without buying us anything.
- **Critical path:** Phase 0 (scaffolding with D1 + R2 + Hono + Tailwind on a deployed URL) → Phase 1 (read-only public feed with SEO/AEO foundation). Once a deployed URL serves a real, crawlable feed of Windsor events, every later phase (Interested, curator auth, filtering) layers onto a working surface. PRD §8 Week 2 = Phase 1 done.
- **What was deferred to later phases on purpose:**
  - **Interest mechanics** (PRD Must #3) → Phase 2. Not needed for the Week 2 milestone read-only feed.
  - **Curator auth + posting + image upload** (PRD Must #4–6) → Phase 3. The Week 2 milestone is hand-seeded events.
  - **Filtering and "this weekend" toggle** (PRD Should #7, #8) → Phase 4. Should-stories, not Must.
  - **Manual dark-mode toggle, real date picker, illustration system** → out of scope per DESIGN §8, not in any phase.
  - **SEO/AEO foundation is NOT deferred** — it lands in Phase 1 per the developer's explicit ask and PRD §8 ("harder to bolt on later").
- **`/clear` discipline:** Every phase boundary is an explicit context reset. Start each phase in a fresh Claude Code session that loads only the "Context to load" lines below. Do not carry the previous phase's transcript forward — that's the whole point of this plan.

---

## Phases

Each phase below follows the same structure. Skip what doesn't apply but keep the headers so future-you (or future-Claude) can scan.

### Phase 0 — Scaffolding

**Goal:** A "Hello Windsor" page renders at a public Cloudflare Worker URL, with D1 + R2 + Hono + React + Tailwind + Vitest wired, a smoke test green, and the rendering architecture decided.

**Context to load:**
- `CLAUDE.md` (root + project)
- `docs/PRD.md` §6 (technical shape, Cloudflare stack table)
- `docs/DESIGN.md` §3 (component approach) and §4 (visual tokens — needed to seed `tailwind.config.ts` with the palette)
- Cloudflare `llms.txt` index → fetch the Workers + Static Assets + D1 + R2 sections only (per `CLAUDE.md` rule)

**Files this phase creates/modifies:**
- `wrangler.toml` — Worker config; D1 binding `DB`; R2 binding `IMAGES`; Static Assets binding; `SESSION_SECRET` placeholder
- `package.json` — deps: `hono`, `react`, `react-dom`, `@cloudflare/vitest-pool-workers`, `tailwindcss`, `@headlessui/react`, `@heroicons/react`, `typescript`, `wrangler`
- `tsconfig.json`
- `vitest.config.ts`
- `tailwind.config.ts` — palette tokens from DESIGN §4 (teal, slate, semantic colors); `darkMode: 'media'`
- `src/styles/globals.css` — Tailwind directives
- `src/index.ts` — Hono app entry, root route renders a server-side "Hello Windsor" page
- `src/types.ts` — `Env` binding types
- `src/db/schema.sql` — empty placeholder (real migrations land in Phase 1)
- `README.md` — add deploy URL, dev/test commands
- `.dev.vars` (gitignored) — local secrets placeholder

**Tests this phase adds:**
- `tests/smoke.test.ts` — `GET /` returns 200 and contains "Windsor"
- `tests/bindings.test.ts` — `env.DB` runs `SELECT 1`; `env.IMAGES` lists objects without throwing

**Done-when:**
- [ ] `npm test` passes locally.
- [ ] `wrangler deploy` produces a public URL.
- [ ] URL is in `README.md`.
- [ ] D1 database created (`wrangler d1 create`) and bound; R2 bucket created and bound.
- [ ] Tailwind builds; teal `bg-teal-600` and dark `bg-slate-950` both render correctly in a browser preview.
- [ ] Rendering architecture decision recorded in the decision log (see Risks).

**Session budget:** 1–2 sessions.

**Risks / unknowns:**
- **Rendering architecture (load-bearing).** Decide in this phase, not later. Two viable paths:
  - **(a) Hono JSX server-rendered + minimal client islands** — server renders HTML via Hono's JSX; the few interactive components (`InterestedButton`, filter chips, `WeekendToggle`) ship as small client bundles. Cheapest for SEO/AEO, lightest runtime, easiest on Workers limits. **Recommended default.**
  - **(b) Full React SSR with `react-dom/server` + hydration.** More familiar, more complex, more bundle. Justify only if you want a real SPA feel.
  - Whichever is chosen, write it into the decision log so Phase 1 doesn't re-litigate.
- Workers Static Assets vs Pages: confirm Workers + assets in `wrangler.toml` per PRD §6. Don't accidentally split into a Pages project.

---

### Phase 1 — Read-only public feed + SEO/AEO foundation

**Goal:** A Windsor resident opens the deployed URL on a phone, sees a hero block of the 5 soonest upcoming events plus a paginated chronological tail of the rest, and can tap into a detail page for any event. The site is server-rendered, has a valid sitemap, robots.txt, `llms.txt`, and Event JSON-LD on every detail page. Covers PRD Must #1 and #2; satisfies PRD §8 Week 2 milestone.

**Context to load:**
- `docs/PRD.md` §4 stories 1, 2; §8 Week 2 milestone; §7 (AEO unknown)
- `docs/DESIGN.md` §1 (mood + anti-references), §2 (IA, hero, pagination, empty states), §3 (component organization, no rich text), §4 (tokens), §5 (a11y — WCAG 2.2 AA), §6 (responsive)
- `src/index.ts`, `wrangler.toml`, `tailwind.config.ts` (from Phase 0)
- The Phase 0 decision-log entry on rendering architecture

**Files this phase creates/modifies:**
- `src/db/migrations/0001_events.sql` — `events` table: `id` (ULID text PK), `title`, `description`, `image_key` (R2 key, nullable for now — uploader lands Phase 3), `starts_at`, `ends_at`, `location`, `event_type`, `family_friendly` (bool), `curator_id` (nullable for hand-seeded), `created_at`, `updated_at`
- `src/db/queries/events.ts` — `upcoming(limit, offset)`, `byId(id)`, `count()`
- `src/db/seed.ts` — script that inserts ~5–10 real Windsor events
- `src/components/atoms/Button.tsx`, `Badge.tsx`, `IconButton.tsx`
- `src/components/molecules/EventCardMeta.tsx`, `FormField.tsx` (used in Phase 3 — scaffold now)
- `src/components/organisms/AppHeader.tsx`, `EventCard.tsx`, `EventFeed.tsx`, `EventDetail.tsx`
- `src/routes/index.tsx` — `/` (hero + tail + Load More)
- `src/routes/events/[id].tsx` — detail page
- `src/routes/sitemap.xml.ts` — dynamic sitemap from D1
- `src/routes/robots.txt.ts` — allows all, points to sitemap
- `public/llms.txt` — site description + key URLs for LLM crawlers
- `src/lib/structured-data.ts` — Schema.org `Event` JSON-LD generator
- `src/lib/dates.ts` — "today / this weekend / next 7 days" helpers
- `tests/feed.test.ts`, `tests/detail.test.ts`, `tests/sitemap.test.ts`, `tests/seo.test.ts`

**Tests this phase adds:**
- `GET /` returns 200; HTML contains ≥1 seeded event title.
- `GET /` hero region contains at most 5 events; Load More query (`?offset=5`) returns the next batch.
- `GET /events/:id` for a seeded ID returns 200 with title in HTML.
- `GET /events/:bad-id` returns 404.
- `GET /sitemap.xml` includes every upcoming event URL.
- Detail page contains a valid `<script type="application/ld+json">` block with required Event fields.
- Empty-state copy renders when no events are seeded (test against an empty D1).

**Done-when:**
- [ ] Mobile browser at 320px renders the feed cleanly in light and dark.
- [ ] Detail page renders with image fallback (no R2 image yet — uses typographic fallback per DESIGN §7 mitigation).
- [ ] `Load More` paginates the tail.
- [ ] Lighthouse SEO score ≥ 95 on `/` and a detail page (run from production URL, not localhost).
- [ ] `sitemap.xml`, `robots.txt`, `llms.txt` all live and valid.
- [ ] `npm test` passes; `wrangler deploy` succeeds.

**Session budget:** 2 sessions. Big phase because SEO/AEO + feed are bundled. **Natural sub-split if it overruns:** Phase 1a = feed + detail (server-rendered, dark mode), Phase 1b = sitemap + robots + llms.txt + JSON-LD + Lighthouse pass. Only split if 2 sessions clearly won't fit.

**Risks / unknowns:**
- `llms.txt` conventions in 2026 are still evolving. Worth a quick web search at phase start before writing the file.
- Lighthouse SEO scoring can be picky about meta tags (`og:`, `twitter:`, canonical, lang). Budget time for iteration; don't ship until ≥ 95.
- Hand-seeded events need real Windsor data — author must collect ~5–10 actual events before this phase ends. If supply isn't available, this is the PRD §3 kill-criterion tripwire showing up early.

---

### Phase 2 — "Interested" mechanic

**Goal:** A browser opens an event detail page, clicks "Interested," and the click is recorded against an anonymous cookie ID. The understated count near the button reflects the new total. Click again from the same browser → no double-count. Covers PRD Must #3 and starts feeding the §3 success metric (50 unique users with ≥1 interest in 3 months).

**Context to load:**
- `docs/PRD.md` §4 story 3; §3 success metric; §5 ("Browser account creation" → anonymous-ID approach); §6 (sessions / cookies note)
- `docs/DESIGN.md` §1 anti-references (interest is NOT a social signal); §2 (count rendered near button, detail page only — never on feed cards)
- `src/db/queries/events.ts`, `src/routes/events/[id].tsx` (Phase 1)
- The Phase 0 rendering-architecture decision

**Files this phase creates/modifies:**
- `src/db/migrations/0002_interests.sql` — `interests` table: `anon_id`, `event_id`, `created_at`; unique on `(anon_id, event_id)` for idempotence
- `src/db/queries/interests.ts` — `countForEvent(eventId)`, `upsert(anonId, eventId)`, `hasMarked(anonId, eventId)`
- `src/lib/cookies.ts` — read/write `anon_id` cookie (HTTP-only, SameSite=Lax, ~2-year expiry)
- `src/lib/middleware.ts` — middleware that mints an `anon_id` if missing
- `src/components/molecules/InterestedButton.tsx` — client component; POSTs to API, optimistic update; respects `hasMarked` state
- `src/routes/api/events/[id]/interest.ts` — POST handler
- `src/routes/events/[id].tsx` — update to render button + count; mark "Interested ✓" state if cookie says so
- `tests/interest.test.ts`

**Tests this phase adds:**
- `GET /` sets `anon_id` cookie if missing; preserves it if present.
- `POST /api/events/:id/interest` records a row; count increments by 1.
- Duplicate POST with same `anon_id` → no new row; count unchanged (idempotent).
- POST with no cookie → server mints one and records the interest (no friction for first-time users).
- `GET /events/:id` HTML reflects the current count; if the cookie has marked it, the button renders the "Interested ✓" state.

**Done-when:**
- [ ] Click "Interested" → count goes up; refresh → count and "Interested ✓" state persist for that browser.
- [ ] Open in a private window (different `anon_id`) → count goes up by 1 more.
- [ ] Same browser click twice → count unchanged.
- [ ] Count NEVER appears on a feed card — only on detail (verify visually).
- [ ] `npm test` passes; `wrangler deploy` succeeds.

**Session budget:** 1 session.

**Risks / unknowns:**
- Cookie clearing → new `anon_id` → recounted as a new user. Accepted: not solving cross-device or cross-clear identity in v1.
- A bot or someone with curl could inflate counts. Accepted in v1 (PRD §6 has no rate-limiting story); revisit only if it actually happens.

---

### Phase 3 — Curator auth + event posting (with image upload)

**Goal:** An invited curator visits `/admin/login`, signs in with email + password, lands on `/admin`, creates an event with a featured image, and can edit or delete events they own. The new event shows up on `/` for browsers. Covers PRD Must #4, #5, #6; satisfies PRD §8 Week 3 milestone.

**Context to load:**
- `docs/PRD.md` §4 stories 4–6; §6 (auth + D1 sessions + R2)
- `docs/DESIGN.md` §2 (admin routes `/admin/login`, `/admin`, `/admin/events/new`, `/admin/events/:id`), §3 (admin form, image uploader, native `datetime-local`), §5 (a11y — labels on every input), §8 (no rich text)
- All Phase 1 + Phase 2 files (the admin form mirrors the public detail page schema)

**Files this phase creates/modifies:**
- `src/db/migrations/0003_curators.sql` — `curators` table: `id`, `email`, `password_hash`, `name`, `created_at`
- `src/db/migrations/0004_sessions.sql` — `sessions` table: `token` (PK), `curator_id`, `created_at`, `expires_at`
- `src/db/queries/curators.ts`, `src/db/queries/sessions.ts`
- `src/lib/auth.ts` — PBKDF2 password hash + verify via Web Crypto (no bcrypt on Workers); session token mint/validate
- `src/lib/r2.ts` — image upload helper, key generation, served-URL helper
- `src/middleware/requireCurator.ts` — redirects to `/admin/login` if no valid session
- `src/components/molecules/ImageUploader.tsx` — file input + preview + 2 MB client-side cap
- `src/components/organisms/AdminEventForm.tsx` — all fields, used by both new and edit
- `src/routes/admin/login.tsx` — form + POST handler; sets session cookie
- `src/routes/admin/logout.ts` — clears session
- `src/routes/admin/index.tsx` — curator's own events list (reuses `EventCard`)
- `src/routes/admin/events/new.tsx` — create form
- `src/routes/admin/events/[id].tsx` — edit/delete form
- `src/routes/api/admin/events/[id].ts` — PUT, DELETE
- `src/routes/api/admin/events/index.ts` — POST (create)
- `src/routes/api/admin/upload.ts` — multipart upload to R2
- Update `src/components/organisms/AppHeader.tsx` — swap "Sign in" for "New event" + "Sign out" when authed
- `src/db/seed.ts` — add at least one curator (the author) with a known password
- `tests/auth.test.ts`, `tests/admin-events.test.ts`, `tests/upload.test.ts`

**Tests this phase adds:**
- Login with correct password → session cookie set; login with wrong password → 401, no cookie.
- `GET /admin` without session → 302 to `/admin/login`.
- Curator A cannot edit Curator B's event → 403.
- `POST /api/admin/events` with valid session creates the event and it appears on `/`.
- `DELETE /api/admin/events/:id` removes the event AND cascades to `interests` rows.
- Image upload >2 MB → 413 with a readable error.
- Edit-form pre-fills with current values.

**Done-when:**
- [ ] Author logs in, posts a new event (with image), sees it on `/`.
- [ ] Author edits the event; changes appear on `/`.
- [ ] Author deletes the event; it disappears from `/` and from any "Interested" counts.
- [ ] A second seeded curator account can also log in (proves auth is multi-curator, not single-user).
- [ ] `npm test` passes; `wrangler deploy` succeeds.

**Session budget:** 2 sessions. Big phase. **Natural sub-split if it overruns:** Phase 3a = auth + create text-only event + admin list; Phase 3b = edit + delete + image upload.

**Risks / unknowns:**
- **No bcrypt on Workers runtime.** Use PBKDF2 via Web Crypto (`SubtleCrypto.deriveBits`) with ~100k iterations and a per-user salt. Don't hand-roll the crypto from scratch — copy a small known-good helper from Workers docs / examples.
- **R2 image handling.** v1 cap = 2 MB inline PUT; no multipart. Resize/transform is out of scope (DESIGN §8); if image quality is bad we either accept it or add a server-side `<img>` resize via Cloudflare Images in v2.
- Curator account provisioning. No signup, no password reset (PRD §6) — curators are added by editing the seed script and re-running. Be explicit in README about this.

---

### Phase 4 — Filtering + weekend toggle + demo polish

**Goal:** Browsers can filter the feed by event type and toggle "this weekend only"; the feed reads filter state from URL params (deep-linkable). Mobile UX is tightened across 320 / 768 / 1024 in both light and dark. Real curators have posted real events (supply test). Site appears in at least one LLM answer to "what's going on in Windsor this weekend?". Covers PRD Should #7, #8; satisfies PRD §8 Week 4 demo.

**Context to load:**
- `docs/PRD.md` §4 stories 7, 8; §8 Week 4 milestone; §3 success metric
- `docs/DESIGN.md` §1 (Airbnb-spirit filtering, NOT Airbnb-surface), §2 (filter chips inside the feed page; filtered empty state), §6 (responsive QA targets)
- `src/routes/index.tsx`, `src/db/queries/events.ts` (Phase 1)
- `src/components/molecules/FilterChip.tsx` scaffold (Phase 1)

**Files this phase creates/modifies:**
- `src/components/molecules/FilterChip.tsx` — wire up (multi-select event-type chips)
- `src/components/molecules/WeekendToggle.tsx` — Headless UI `Switch`
- `src/lib/filters.ts` — parse/serialize filter state to/from URL params
- `src/db/queries/events.ts` — extend `upcoming()` to accept `{ types?, weekendOnly? }`
- `src/routes/index.tsx` — read URL params, pass filters down, render filtered empty state
- `tests/filters.test.ts`

**Tests this phase adds:**
- `GET /?type=tech` returns only `event_type=tech` events.
- `GET /?weekend=1` returns only events in current Fri 00:00 → Sun 23:59 (Windsor / Mountain Time).
- Multiple `?type=` values combine as OR within the type facet.
- Filtered-to-nothing renders the "no events match these filters" empty state with a Clear button (DESIGN §2).
- Filter state survives a Load More click (next-page URL preserves params).

**Done-when:**
- [ ] Filter chips work end-to-end on mobile.
- [ ] "This weekend" toggle works; copy is correct relative to the actual current weekend.
- [ ] Empty filtered state renders with a working "Clear filters" button.
- [ ] **Supply test (live, off-app):** ≥ 3 invited curators (not just the author) have logged in and posted ≥1 event each.
- [ ] **Indexing test (live, off-app):** Google `site:` search returns at least one event page; at least one LLM (ChatGPT, Perplexity) cites the site in answer to *"what's going on in Windsor this weekend?"*.
- [ ] Mobile QA pass: visually verify 320px, 768px, 1024px in light and dark.
- [ ] `npm test` passes; `wrangler deploy` succeeds.
- [ ] Demo walkthrough rehearsed end-to-end on a real phone.

**Session budget:** 1 session (the code work; the supply + indexing tests aren't session work, they're calendar work).

**Risks / unknowns:**
- **LLM AEO visibility is partly out of our control.** If the site doesn't appear in LLM answers by demo day, that's not a code bug — it's a discoverability gap. Don't hold the demo for it.
- **Supply risk (PRD §3 kill criterion).** If curators don't actually post by this phase, the demo lacks substance. Mitigation already in plan: author seeds events in Phase 1 and continues seeding through Phase 4 as a backstop.
- Weekend window depends on timezone. Server is UTC; Windsor is Mountain Time — use explicit `America/Denver` arithmetic, not `new Date()` math.

---

## Decision log

A short append-only log of when the plan changed and why. Helps future-you understand why the current phase numbering exists.

| Date | Phase touched | Change | Reason |
|---|---|---|---|
| 2026-05-13 | All | Initial 5-phase plan committed | Vertical slicing chosen over horizontal; SEO/AEO folded into Phase 1 per developer ask; image upload kept inside Phase 3 rather than split out. |

---

## Handoff notes

_What state should the repo be in when this plan is "done"? Used to verify the project shipped._

- Public URL deployed and linked from README.
- All Must-have user stories from PRD.md §4 have green tests.
- Architecture diagram regenerated and committed.
- Demo video and PRD video linked from README.
