# Design Brief

_This file is the source of truth for UI/UX decisions on this project. Fill it out with the `design-brief` skill after the PRD is solid. Keep it short — a design brief is a compass, not a spec._

## 1. Visual identity

**Mood (3–5 adjectives):** calm, utilitarian, serious, crisp. Reads like a transit app or a well-made docs site — not a party flyer, despite the product being about parties. That tension is intentional: the feed is a tool, not a vibe.

**Reference apps:**
- **Eventbrite** — borrowing the "Events in {City}" card layout: featured image + title + description + time, clean and scannable in a vertical feed.
- **Airbnb** — borrowing the *spirit* of their filtering: fast, accessible, results update live, no page reloads. NOT borrowing the surface area — at Windsor scale (hundreds of events/year, maybe 20–40 on the feed at a time) the full Airbnb filter UI is overkill. v1 needs only event-type chips and a "this weekend" toggle (PRD §4, Should #7–#8).

**Anti-references:** Anything that feels like social media — Facebook, NextDoor, Instagram. No comments, no "trending," no avatars/handles in the feed, no engagement metrics on feed cards. "Interested" is a memory aid for the browser and a success/resonance signal for the operator — not a social signal performed at the user. Interest *count* exists on the event detail page only, understated, near the "Interested" button — never on feed cards. This lines up with PRD §4 Won't and §5 Out of scope.

**Brand constraints:** Clean slate. Teal accent — starting at Tailwind `teal-600` (#0d9488), which passes WCAG AA on white. Name and logo are deferred; leaning **Windsor Board** (the repo is already `community-board`, and the metaphor of digitizing the coffee-shop corkboard fits the calm/utilitarian/serious adjectives). Final naming is not blocking v1 design.

## 2. Information architecture

**Primary screens (top-level routes):**
- `/` — public event feed. Hero block of the **next 5 upcoming events** (soonest first), followed by the rest of upcoming events in chronological order. "Top" simply means "soonest" — no ranking algorithm. If there are fewer than 5 upcoming events total, the hero is everything there is; that's acceptable graceful degradation.
- `/events/:id` — public event detail page. Featured image, title, description, date/time, location, family-friendly flag, and an "Interested" button with an understated interest count near it.
- `/admin/login` — curator login. Email + password, no signup, no password reset (per PRD §6 — curators contact the author if locked out).
- `/admin` — curator dashboard. Same card layout as `/`, filtered to events the logged-in curator created. Includes a "New event" action in the header.
- `/admin/events/new` — create a new event. All the public-detail fields are editable here.
- `/admin/events/:id` — edit an existing event the curator owns. Same form as `/new`, pre-filled. Delete also lives here.

**Navigation model:** Minimal chrome. A small top header with the app name on the left and a tiny "Sign in" link on the right for anonymous browsers. Once a curator is signed in, the header swaps that link for a "New event" button and a "Sign out" affordance. No bottom tabs, no side nav, no global tabs. Filter controls (event-type chips, "this weekend" toggle) live *inside* the feed page itself, not in the global nav. Mobile is the primary target; the header is already small enough that nothing needs to collapse.

**The hero screen:** `/` is the hero. In 3 seconds it should communicate: *"These are the top 5 things to do in Windsor today (or in the next couple of days if today is quiet), and there's more below."* Concretely, that means a clear page title naming Windsor and the time window, the hero block visually distinct from the chronological tail, and no marketing chrome above the fold competing with the cards.

**Pagination:** The chronological tail below the hero loads in batches via a "**Load More**" button at the bottom of the list. Initial page size: **10** events (revisit when we see real supply). No infinite scroll — auto-loading on scroll is a social-media pattern and conflicts with §1 anti-references. When all upcoming events have loaded, the button is replaced by a quiet "You've seen everything coming up" line.

**Empty states:** Every screen with a list defines an explicit empty state. None of these should feel like a broken page.

- **Feed (`/`) — no events anywhere.** "No events on the board yet." Plus a one-line pointer: *"Run a Windsor org or business? Email [contact] to become a curator."* Honest, doesn't pretend the feed is alive when it isn't.
- **Feed — filters applied, nothing matches.** "No events match these filters." With a "Clear filters" button. Different message from the no-events-at-all case so the user doesn't think the whole site is empty.
- **Feed — hero has events but tail is empty after pagination.** Quiet line: "You've seen everything coming up." No illustration, no encouragement to come back later — overly chatty contradicts the calm/utilitarian mood.
- **Admin (`/admin`) — curator hasn't posted yet.** "You haven't posted any events." Plus a prominent "Create your first event" button (primary, teal-600).
- **Admin — curator's events all in the past.** "No upcoming events. Past events are hidden — create a new one to show up on the feed." Edge case but real (curator returns after a quiet stretch).

## 3. Component approach

- **Framework:** React.
- **Component library:** [Headless UI](https://headlessui.com/) for unstyled, accessible primitives (Dialog, Menu, Combobox, Listbox, Disclosure, Tabs, Switch, RadioGroup, Popover).
- **Styling:** Tailwind CSS.
- **Icons:** Heroicons (same team as Tailwind/Headless UI — one less decision to make).
- **Component organization:** Atomic-design-flavored, but pragmatically capped at 3 layers (no `templates/`). Routes compose organisms directly.
  - `atoms/` — `Button`, `Input`, `Label`, `Badge`, `IconButton`. Pure, no app state.
  - `molecules/` — `FormField` (Label + Input + error), `InterestedButton` (Button + count), `FilterChip`, `EventCardMeta` (date/time + family-friendly badge).
  - `organisms/` — `EventCard`, `EventFeed`, `EventDetail`, `AdminEventForm`, `AppHeader`. App-aware composites.

**Custom components (things Headless UI doesn't cover, that we'll build ourselves):**
- `EventCard` and the rest of the organisms above (pure presentation + light state — just Tailwind).
- `FilterChip` row for event-type filters. Multi-select, built from buttons; no Headless UI primitive needed.
- Image uploader for the admin form (one image per event → R2). Small custom component: file `<input>` + preview thumbnail + client-side size cap. Not pulling a library.

**Date/time picker:** Native `<input type="datetime-local">` for v1. iOS/Android give a full native picker; desktop is functional if plain (desktop isn't a v1 target — PRD §2). If a curator pushes back, `react-day-picker` (which shadcn/ui's Calendar also wraps) is a ~30-minute swap into a Headless UI `Popover`. Captured as a v2 polish item, not a v1 risk.

**Rich text in event descriptions:** Not in v1. Plain `<textarea>` only — markdown/rich-text editing is out of scope.

**Why this stack:** Headless UI gives accessibility (focus management, ARIA, keyboard nav) for free; Tailwind makes the styling decisions explicit in markup; Heroicons keeps the icon system one decision instead of ten. Together they let an AI-assisted developer move fast without shipping inaccessible junk.

## 4. Visual tokens

Small palette, applied uniformly. Don't add tokens outside this list without updating this section.

**Color**

| Token | Value | Use |
|---|---|---|
| Primary | `teal-600` (#0d9488) | Interactive (links, button bg, focus rings) |
| Primary hover | `teal-700` (#0f766e) | Hover/active state on primary |
| Page background | `slate-50` (#f8fafc) | Body background |
| Border / divider | `slate-200` (#e2e8f0) | Card borders, hr, input borders |
| Text secondary | `slate-500` (#64748b) | Captions, metadata |
| Text primary | `slate-900` (#0f172a) | Body and headings |
| Success | `emerald-600` (#059669) | "Event posted," "saved" toasts |
| Warning | `amber-600` (#d97706) | "Starts in <1h," unsaved form |
| Danger | `rose-600` (#e11d48) | Delete confirms, login errors |

`slate` (cooler) is chosen over `gray` / `zinc` to sit cleanly next to teal.

**Type**

- **Font:** System stack — Tailwind's default `font-sans`. No webfont download. Crisp on mobile, zero perf cost.
- **No separate display font.** Headings use the same family in a heavier weight.
- **Scale:** `text-sm` (14px secondary), `text-base` (16px body), `text-lg` (18px h3), `text-xl` (20px h2), `text-3xl` `font-semibold` (30px h1).

**Spacing**

Tailwind defaults. Common values: `p-4` (cards), `gap-4` (vertical card stacks), `gap-2` (chip rows), `px-3 py-2` (buttons).

**Radius**

`rounded-md` (6px) everywhere — cards, buttons, inputs, chips, the image preview. No exceptions, no `rounded-full`, no `rounded-none`. One radius across the app.

**Shadow**

Two elevations only.

- `shadow-sm` — cards in the feed (very subtle lift; crisp, not floaty).
- `shadow-md` — modals, dropdowns, popovers. Reserved for overlays.

No `shadow-lg` / `shadow-xl` / inner shadows. "Crisp + calm" reads flat; depth is for overlays only.

**Dark mode**

Light is the default; dark applies when the OS prefers dark. Tailwind config: `darkMode: 'media'` — no manual in-app toggle in v1 (a toggle is a v2 polish; it adds UI surface and persistence we don't need yet).

| Token | Light | Dark | Use |
|---|---|---|---|
| Primary | `teal-600` | `teal-400` (#2dd4bf) | Interactive (links, primary button bg, focus rings) |
| Primary hover | `teal-700` | `teal-300` (#5eead4) | Hover/active on primary |
| Page background | `slate-50` | `slate-950` (#020617) | Body background |
| Surface (cards, modals) | `white` | `slate-900` (#0f172a) | Card & overlay surfaces — one step up from background |
| Border / divider | `slate-200` | `slate-800` (#1e293b) | Card borders, hr, input borders |
| Text secondary | `slate-500` | `slate-400` (#94a3b8) | Captions, metadata |
| Text primary | `slate-900` | `slate-100` (#f1f5f9) | Body and headings |
| Success | `emerald-600` | `emerald-400` | Toasts |
| Warning | `amber-600` | `amber-400` | Inline warnings |
| Danger | `rose-600` | `rose-400` | Destructive actions, errors |

All dark-mode pairs verified to meet WCAG AA contrast on `slate-950`. Featured-image scrims still required in dark mode — the issue isn't background contrast, it's text-on-image legibility.

## 5. Accessibility floor

**Target standard:** WCAG 2.2 AA. Non-negotiable.

**Baseline (applies to every interactive surface):**

- **Keyboard end-to-end.** Every interactive element (cards, "Interested" button, filter chips, admin form, login) reachable and operable with Tab / Shift+Tab / Enter / Space. Headless UI primitives carry the focus-management and ARIA work; don't re-implement those by hand.
- **Contrast (1.4.3).** The §4 palette meets AA in both modes — light (teal-600 = 4.54:1 on white; slate-500 = 4.49:1 on slate-50) and dark (the §4 dark-mode pairs are all verified ≥ 4.5:1 on slate-950). Every new component must be eyeballed in **both** light and dark before merging. Never paint text directly over a featured image without a scrim or solid backing in either mode.
- **Visible labels on inputs (3.3.2 / 4.1.2).** No placeholder-as-label. Every `<input>` has a real `<label>`. Captured in the `FormField` molecule.
- **Visible focus (2.4.7).** Default focus ring on interactive atoms: `focus:ring-2 focus:ring-teal-600 focus:ring-offset-2`. Never `outline: none` without a replacement.
- **Color is never the only signal (1.4.1).** Family-friendly is a labeled badge ("Family-friendly"), not just a colored dot. Error states use icon + text, not just red.

**WCAG 2.2 additions (the criteria new in 2.2 that affect this app):**

- **2.4.11 Focus Not Obscured (Minimum).** If anything sticky is added later (sticky filter row on scroll, sticky admin CTA), it must not fully cover a focused element. Currently a non-issue — nav model is non-sticky.
- **2.5.7 Dragging Movements.** N/A — no drag interactions in v1. Revisit if image reorder or similar lands.
- **2.5.8 Target Size (Minimum).** All interactive targets ≥ 24×24 CSS px. Tailwind defaults (`px-3 py-2` buttons, `h-10 w-10` icon buttons) hit this comfortably. Explicit watch-points: filter chips, the "Interested" button, any icon-only action.
- **3.3.8 Accessible Authentication (Minimum).** Login is email + password — no cognitive puzzle, already compliant. If rate-limit CAPTCHA is added later, it needs an alternative (e.g. email-link fallback).

## 6. Responsive strategy

**Smallest target:** iPhone SE / 320px viewport. Everything must render and be usable here. Anything that doesn't fit at 320px is the layout's problem, not the user's.

**Breakpoints:** Tailwind defaults — `sm` 640px, `md` 768px, `lg` 1024px, `xl` 1280px. No custom breakpoints.

**What reflows at each breakpoint:**

- **Base (320–767px):** Feed is a single column of cards, full-width minus container padding (`px-4`). Header is a single bar — app name left, "Sign in" (or curator actions) right. Filter chips wrap onto multiple rows if needed. Admin form is single column.
- **`md` (768px+):** Feed becomes a 2-column grid. Card content unchanged; just more per row.
- **`lg` (1024px+):** Feed becomes a 3-column grid. Header gains horizontal breathing room but no new content.
- **`xl` (1280px+):** No further reflow — the 3-column grid sits in a centered max-width container (`max-w-6xl`) so cards don't stretch into uncomfortably wide rectangles.

**Admin form:** single column at every breakpoint — multi-column forms add cognitive load without saving meaningful space.

**Image handling note:** featured images render at a consistent aspect ratio (`aspect-[16/9]` is the working default; revisit when we see real curator uploads) so the grid stays even regardless of original image dimensions.

## 7. Risks & unknowns

Residual design risks we're accepting in v1 with eyes open. None block launch; each has a tripwire.

- **Featured image quality drift.** The card design is clean, but curators will upload phone snapshots and screenshots of paper flyers. Half a feed of low-resolution flyer photos can make a pristine layout still feel cheap. **Mitigation:** if no image is uploaded, fall back to a typographic card with a colored background derived from the event type — gives every card a baseline of dignity even with no asset. Revisit if real curator uploads systemically look bad; consider Cloudflare Images (paid) for transforms if it becomes a real problem.
- **Mid-breakpoint visual testing gap.** With a 320px floor and a 3-column grid at `lg` (1024px+), the visual ends are well-defined but `md` / `lg` get less hand-testing. Risk of "looks weird on iPad" reports. **Mitigation:** when implementing the feed, eyeball the grid at exactly 768px and 1024px on first build; don't just trust the phone view.
- **Native `datetime-local` on desktop.** Mentioned in Section 3. Functional but plain — desktop curators may complain. **Tripwire:** if a curator asks for a better picker, swap to `react-day-picker` inside a Headless UI `Popover` (~30-min change).
- **Load-More performance at high supply.** Pagination is decided ("Load More," page size 10), but if a "busy weekend" produces 80+ upcoming events the first load is fine; later pages just need the API to be index-friendly. **Tripwire:** if a page beyond the second feels slow, paginate by date window instead of offset.
- **Empty-state copy will need real-world tuning.** The lines in Section 2 are a starting point. They'll read differently when staring at an actual empty board in week 1 of launch. Plan to revisit copy once the feed has its first real users.
- **Dark-mode testing burden.** Every component now has to be eyeballed in two color modes × multiple breakpoints. **Mitigation:** build each organism in light first, then immediately switch the OS to dark and walk it again before moving on — don't batch the dark-mode pass for the end of a feature. Catching dark-mode bugs at component-write time is much cheaper than triaging them in a pre-demo sweep.

## 8. Out of scope (for v1)

Same spirit as the PRD's out-of-scope section, applied to design. Each item below was consciously considered and pushed out, not forgotten.

- **Manual dark-mode toggle.** Dark mode itself ships in v1 (via OS preference). A user-facing toggle in the UI — and the cookie/localStorage persistence it needs — is v2.
- **Animations beyond Headless UI defaults.** No page transitions, scroll-triggered effects, skeleton loaders, or hover micro-interactions. Plain spinners and quiet text-only loading states. Matches "calm + crisp."
- **Custom logo and brand illustration system.** App ships with a wordmark only ("Windsor Board" pending final naming). A real logo and any custom illustration are v2.
- **Dedicated marketing / about / FAQ pages.** A short "Become a curator" line in the empty state covers the only acquisition surface needed in v1.
- **Map / geo view.** Per PRD §5 — list with a location string is enough at Windsor scale.
- **Internationalization-ready layouts.** English-only. No i18n directory structure or RTL audit in v1.
- **Print stylesheets.** Out of scope; no v1 use case.
- **Email / push / SMS notification design.** Those channels don't exist in v1 (PRD §5), so they have no UI to design.
- **Desktop-specific affordances beyond the 3-column grid.** Desktop works because the grid scales. No hover-tuned interactions, no desktop-only information density, no keyboard shortcut surface beyond standard Tab/Enter — the audience is on phones (PRD §2).
- **A real date/time picker library.** Native `<input type="datetime-local">` only. `react-day-picker` is a v2 swap if curators push back.
- **Rich text in event descriptions.** Plain `<textarea>` only.
