---
name: build-plan
description: Turn a PRD and design brief into a phased build plan that fits inside Claude Code's context window. Use this skill when the developer asks "how should I build this", "what should I do first", "how do I break this into chunks", "make a build plan", or whenever they have a stable PRD + DESIGN.md and are about to start implementing. Run this AFTER pm-interview and design-brief; re-run whenever the plan diverges from reality. Output is `docs/BUILDPLAN.md`.
---

# Build Plan

Your job is to slice the project into phases that each fit comfortably inside one Claude Code session — small enough to load only the relevant context, big enough to ship a real outcome. The output is a written plan in `docs/BUILDPLAN.md`.

A build plan is not a Gantt chart. It's a **token-economy artifact**: each phase tells future-you (and future-Claude) exactly which files and which doc sections to load to execute it, so you never pay to re-derive context you already have.

## When to invoke

- Right after `pm-interview` and `design-brief` are done and you're about to start building.
- When the developer asks "what should I build first?" or "how do I chunk this up?"
- When the in-progress plan has drifted from the actual code — re-running this skill produces a corrected, current plan.

If `docs/BUILDPLAN.md` doesn't exist, create it from the template in the starter repo. If it exists, read it first and only re-plan the phases that have changed.

## Inputs you must read first

Always load and reference:

- `docs/PRD.md` — for must-have user stories (§4), out-of-scope (§5), tech shape (§6), milestones (§8).
- `docs/DESIGN.md` — for component approach (§3), screens / IA (§2), accessibility floor (§5).
- `CLAUDE.md` — for tech stack and any project-specific rules.
- `wrangler.toml` and the existing repo if there's already code, to see what's already scaffolded.

If any of these are missing, stop and tell the developer to run `pm-interview` or `design-brief` first.

## What makes a good phase

A phase is right-sized when:

1. **It has one user-visible outcome.** "Users can submit an event" is a phase. "Refactor the Workers binding" is not — that's an implementation detail of a phase.
2. **It touches a bounded set of files.** A phase that touches 3–8 files is healthy. 20+ files means the phase is actually two phases.
3. **It can be loaded into context cheaply.** You should be able to name in advance which doc sections and files Claude needs. If you can't, the phase is too vague.
4. **It leaves the repo green.** Tests pass at the end of the phase. Deploy works. The phase is mergeable on its own.
5. **It's reversible.** If the phase turns out to be wrong, ripping it out doesn't take other phases with it. Avoid phases that bake assumptions into many later phases.

A phase is wrong-sized when:

- It would take more than ~2 focused Claude Code sessions. Split it.
- It can't be tested without a later phase being done. Reorder.
- It loads the entire project into context. The phase isn't bounded enough.

## The interview arc

Ask **one question at a time**. Update `docs/BUILDPLAN.md` as you go.

### 1. Slicing principle

Pick one. Don't mix.

- "Are we slicing **vertically** (each phase ships one user story end-to-end) or **horizontally** (data layer first, then API, then UI)?"
- For most projects in this course, **vertical is the default** because it produces a deployable thing earlier and aligns with the must-have user stories.
- Horizontal is only right when the data model is genuinely the hardest part and not knowing it blocks UI thinking.

Capture the chosen principle in the Strategy section.

### 2. Identify the critical path

- "Which 1–2 phases unblock the rest?"
- "What's the earliest phase where a real user could see something work?"

The critical path is usually: scaffolding → first real user story → the second user story that exercises the data model.

### 3. List the phases

Walk through `docs/PRD.md` §4 (user stories, MoSCoW order) and turn each Must into a candidate phase. Then:

- **Drop or merge** phases that are too small to stand alone (e.g. a phase that just adds one form field is part of a larger phase).
- **Split** phases that are too big (e.g. "user accounts" is really "signup," "login," "session," and "password reset" — at least three phases).
- **Order** them by critical path, not by importance. The most-important feature might depend on a less-important one.
- **Phase 0** is always scaffolding: bootstrap, smoke test, deploy.

Aim for **3–6 phases** for a 3-week build. More than 8 means the slicing is too fine; fewer than 3 means it's too coarse.

### 4. For each phase, fill out the rubric

For each phase, ask the developer (or propose, then confirm):

- **Goal:** one sentence, user-visible.
- **Context to load:** name the exact PRD sections, DESIGN sections, and files Claude should load. Don't say "the PRD" — say "PRD §4 story 2 and §6."
- **Files this phase creates/modifies:** best-guess list. It's fine if this is partly wrong; updating it after the phase ships is part of the loop.
- **Tests this phase adds:** name them.
- **Done-when:** 3–5 checkboxes a human can verify.
- **Session budget:** rough estimate. `< 1`, `1`, `1–2`, `2+`. If `2+`, the phase is probably too big — challenge it.
- **Risks / unknowns:** what would force a re-plan.

### 5. Capture the handoff state

What does "done" look like for the whole project? Usually:

- Public URL deployed.
- All Must-have stories pass tests.
- Architecture diagram + videos linked from README.

Reference the pass criteria from the syllabus where relevant.

## Token-economy patterns

When writing the plan, optimize for cheap session starts:

- **Co-locate related work.** If two user stories touch the same handler, put them adjacent so you don't re-load that handler twice.
- **Defer cross-cutting concerns.** Auth, logging, error pages — fold these in late, in their own phase, not sprinkled across every other phase.
- **Name explicit `/clear` boundaries.** Every phase boundary is a hint that the developer should `/clear` Claude's context before the next phase. Say so explicitly in the Strategy section.
- **Pre-curate the file list.** The "Context to load" line is the whole point of this skill. The better that list is, the cheaper the session starts.
- **Avoid speculative phases.** Don't plan a phase for "maybe-features" from the PRD's Could / Won't list. They go in the decision log if they ever come back.

## After the interview

1. Read the plan back in 3–5 sentences (the slicing principle, the critical path, the count of phases, the handoff state).
2. Flag the top 1–2 phases most likely to need re-planning.
3. Tell the developer: "Start phase 0 in a fresh session. Load only what `Context to load` lists for that phase."

## Re-running this skill

The plan will drift. That's expected. Re-run this skill:

- After every 1–2 phases ship, to update the next phase's file list with what you actually wrote.
- When a phase turns out to be the wrong size — split or merge.
- When a new requirement enters the PRD.

When re-running, **don't rewrite the whole file from scratch** — read the existing plan, update the affected phases, append to the decision log with the date and reason for the change.

## Ground rules

- **One question at a time.** No interview-dumps.
- **Write as you go.** Update `docs/BUILDPLAN.md` after each section. Show the developer.
- **Cite the docs you're slicing from.** Every phase should be traceable to a PRD section and (if UI) a DESIGN section.
- **Don't design the implementation.** This is a plan, not a design. Files and tests, yes; algorithms, no.
- **Phases are not contracts.** They're best-guess slices. The decision log is where divergence gets recorded honestly.
