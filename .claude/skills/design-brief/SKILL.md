---
name: design-brief
description: Interview the developer about UI/UX decisions and produce a design brief. Use this skill when the developer asks things like "help me design this", "what should this look like", "design my UI", "pick colors / fonts / components", or whenever they're about to build UI for the first time on a project. Run this AFTER the PRD is solid — design decisions ride on top of product decisions, not the other way around.
---

# Design Brief

Your job is to interview the developer about how the app should look, feel, and behave — then write the answers into `docs/DESIGN.md`. The output is a short, opinionated design brief that gives the developer (and you, in future sessions) a compass for UI decisions.

A design brief is a compass, not a spec. Keep it short. Five interesting decisions beat fifty trivial ones.

## When to invoke

- After `pm-interview` produces a PRD and the developer is about to start building UI.
- When the developer asks a one-off design question ("what color should buttons be?") — use this skill to step back, fill the brief, then answer the specific question from inside the brief.
- When a project has UI inconsistencies and the developer asks for help cleaning them up.

If `docs/DESIGN.md` doesn't exist yet, create it from the template at the bottom of this file. If it exists, read it first and only ask about empty sections.

## Defaults (don't re-litigate these unless asked)

- **Framework:** React.
- **Component library:** [Headless UI](https://headlessui.com/) for accessible unstyled primitives (Dialog, Menu, Combobox, Listbox, Disclosure, Tabs, RadioGroup, Switch, Popover).
- **Styling:** Tailwind CSS.
- **Icons:** Heroicons (made by the same team as Tailwind/Headless UI; one less decision).

If the developer pushes back on these, fine — capture the alternative in section 3 of the brief and the reason. Don't argue, but ask once: "what are you hoping to get from a different choice?" Sometimes the answer is "I just want to learn shadcn" and that's a legitimate reason.

## The interview arc

Ask **one question at a time**. Paraphrase before moving on. Update `docs/DESIGN.md` section by section as you go and show the developer what you wrote.

### 1. Mood and references

- "In 3–5 adjectives, what should this feel like?" (calm, editorial, playful, utilitarian, sharp, etc.)
- "Name 1–2 apps whose visual feel you want to borrow from. What specifically — the typography, the density, the color, the motion?"
- "Name an app you do NOT want to feel like, and why."
- "Any brand constraints? Existing logo, colors, stakeholder preferences? 'None' is a valid answer."

**Push back when:** they pick references that don't match their adjectives (e.g. "calm and trustworthy" + "TikTok"). Surface the contradiction; let them pick.

### 2. Information architecture

- "What are the 2–5 top-level screens / routes?"
- "If a user only ever sees one screen, which is it, and what should it communicate in 3 seconds?"
- "Top nav, side nav, or no nav? What collapses on mobile?"

Don't go deeper than top-level. Component-level layout is for when they're building the screen.

### 3. Component approach

Confirm the defaults (React + Headless UI + Tailwind + Heroicons). Then:

- "Anything Headless UI doesn't give you that you'll need to build from scratch?" Common answers: data tables, calendars, rich text editors, file uploaders. Keep this list short — flag each one as a "build vs. find a library" decision.

If they're considering a fuller component library (shadcn/ui, Radix, Mantine, MUI), capture which one and the reason. shadcn is the most common choice for React + Tailwind projects in this course; it composes Headless UI / Radix under the hood, so it's compatible.

### 4. Visual tokens

This is where developers freeze up. Don't let them spend an hour on color theory. Push for **good enough now, refine later**.

- "Pick one accent color. Hex code or 'Tailwind blue-600' is fine."
- "Body font: system stack, or one Google font?"
- "Display font: same as body, or different?"
- "Border radius: sharp (`rounded-none`), soft (`rounded-md`), or pill (`rounded-full`)? Pick one and apply it everywhere."

Capture: primary color, neutral grayscale, one accent, semantic colors (success/warning/danger), 1–2 fonts, one radius value, spacing scale (default to Tailwind's).

### 5. Accessibility floor

Read the standard list out and ask "any you can't commit to?":

- Keyboard-navigable end-to-end.
- WCAG AA contrast on all text.
- Visible labels on form inputs (no placeholder-as-label).
- Visible focus states (don't `outline: none` without replacement).
- Color is never the only way to convey information.

If any will be hard, name it. Don't accept "I'll get to it later" — accessibility regressions are expensive to fix retroactively.

### 6. Responsive strategy

- "Smallest device this needs to work on — phone, iPad, desktop-only?"
- "What's the one thing that should reflow on small screens?" (usually nav → hamburger, multi-column → single column, table → cards)

Default to Tailwind's breakpoints unless they have a specific reason.

### 7. Risks and out of scope

- "What's the design decision you're most worried about?"
- "What are you tempted to build for v1 that you should skip? Dark mode? Animations? Custom illustrations?"

Write the skipped items into the out-of-scope section. Same spirit as the PRD's out-of-scope, applied to design.

## After the interview

1. Read the brief back in 3–5 sentences. Confirm.
2. Flag the top 1–2 design risks.
3. Suggest a concrete next step: "Build the hero screen first. The rest will fall out from how that one feels."

## Ground rules

- **One question at a time.** No interview-dumps.
- **Write as you go.** Update `docs/DESIGN.md` after each section. Show the developer what you wrote.
- **Defaults are defaults.** Don't re-justify React + Headless UI + Tailwind every session. If the developer wants something different, capture the deviation and move on.
- **Don't design the whole system.** A brief, not a design system. Pick enough to be consistent.
- **Cite Headless UI primitives by name** when a UI need maps to one (e.g. "the admin form needs a category picker → Headless UI `Combobox`"). This makes the brief actionable.

## DESIGN.md template (use only if the file doesn't exist)

If `docs/DESIGN.md` is missing, create it using the template that's already in the starter repo at that path. If somehow that's also missing, scaffold one with these sections, in this order:

1. Visual identity (mood, references, anti-references, brand constraints)
2. Information architecture (primary screens, nav model, hero screen)
3. Component approach (framework, library, styling, icons, custom components)
4. Visual tokens (color, type, spacing, radius, shadow)
5. Accessibility floor
6. Responsive strategy
7. Risks & unknowns
8. Out of scope (for v1)
