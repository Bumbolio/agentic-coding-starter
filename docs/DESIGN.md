# Design Brief

_This file is the source of truth for UI/UX decisions on this project. Fill it out with the `design-brief` skill after the PRD is solid. Keep it short — a design brief is a compass, not a spec._

## 1. Visual identity

**Mood (3–5 adjectives):** _e.g. calm, editorial, trustworthy_

**Reference apps:** _1–3 apps whose feel you want to borrow from, and the specific thing you're borrowing._

**Anti-references:** _Apps you do NOT want to feel like, and why._

**Brand constraints:** _Existing logo, colors, fonts, stakeholder preferences. "None" is a valid answer for a fresh project._

## 2. Information architecture

**Primary screens (top-level routes):**
- _e.g. `/` — public event list_
- _e.g. `/admin` — admin dashboard_

**Navigation model:** _Top nav? Side nav? Single page? When does it switch on small screens?_

**The hero screen:** _If a user only ever sees one screen, which is it, and what should it communicate in 3 seconds?_

## 3. Component approach

- **Framework:** React.
- **Component library:** [Headless UI](https://headlessui.com/) for unstyled, accessible primitives (Dialog, Menu, Combobox, Listbox, Disclosure, Tabs, etc.).
- **Styling:** Tailwind CSS unless a strong reason to deviate.
- **Icons:** _e.g. Heroicons, Lucide. Pick one and stick to it._
- **Custom components:** _Anything you'll need to build from scratch because Headless UI doesn't cover it. Keep this list short._

**Why this stack:** Headless UI gives accessibility (focus management, ARIA, keyboard nav) for free; Tailwind makes the styling decisions explicit in markup. Together they let an AI-assisted developer move fast without shipping inaccessible junk.

## 4. Visual tokens

Pick a small palette and stick to it. Don't try to design a full design system — pick enough to be consistent.

- **Color:** _Primary, neutral scale (3–5 grays), one accent, semantic colors (success/warning/danger). Hex codes._
- **Type:** _One body font, one display font (or just one). Sizes: at least body, small, h1–h3._
- **Spacing scale:** _Use Tailwind's defaults unless you have a reason not to._
- **Radius:** _One value across the app (e.g. `rounded-md`). Pick one._
- **Shadow:** _Use sparingly; specify the one or two elevations you allow._

## 5. Accessibility floor

The non-negotiables for this project:

- _e.g. Keyboard navigable end-to-end._
- _e.g. WCAG AA contrast on all text._
- _e.g. Forms have visible labels (no placeholder-as-label)._
- _e.g. Focus states are visible and not removed._
- _e.g. Color is never the only way to convey information._

If any of these will be hard given the project's scope, name it here.

## 6. Responsive strategy

- **Breakpoints:** _Use Tailwind defaults (`sm`, `md`, `lg`, `xl`) unless you have a reason._
- **Smallest target:** _Phone? iPad? Desktop-first? Be explicit._
- **What changes at each breakpoint:** _One sentence per breakpoint about what reflows._

## 7. Risks & unknowns

- _e.g. "Not sure how the admin form will scale to 30 fields — may need to split into multi-step."_
- _e.g. "Image-heavy event cards on slow connections — need a lightweight placeholder strategy."_

## 8. Out of scope (for v1)

Same spirit as the PRD's out-of-scope section but for design:

- _e.g. Dark mode_
- _e.g. Animations beyond default Headless UI transitions_
- _e.g. A logo / custom illustration system_
- _e.g. Internationalization-ready layouts_
