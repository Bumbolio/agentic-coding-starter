# Build Plan

_This file is the phased build plan for the project. It's the bridge between `docs/PRD.md` (what to build) + `docs/DESIGN.md` (what it looks like) and the actual code. Fill it out with the `build-plan` skill after the PRD and design brief are stable. Re-run the skill whenever reality has diverged from the plan._

> **Status:** Draft / In Progress / Complete
> **Last updated:** YYYY-MM-DD
> **Current phase:** _e.g. Phase 2_

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

_Fill in after the build-plan interview. 3–5 sentences max._

- **Slicing principle:** _e.g. "Vertical slices by user story — each phase ships one story end-to-end."_
- **Critical path:** _Which 1–2 phases unblock everything else?_
- **What was deferred to later phases on purpose:** _e.g. auth was punted to phase 4 because the read-only MVP doesn't need it._

---

## Phases

Each phase below follows the same structure. Skip what doesn't apply but keep the headers so future-you (or future-Claude) can scan.

### Phase 0 — Scaffolding

**Goal:** Repo bootstrapped, deploy pipeline working, CLAUDE.md and PRD/DESIGN/BUILDPLAN docs in place.

**Context to load:** `CLAUDE.md`, `docs/PRD.md` (section 6), `docs/DESIGN.md` (section 3).

**Files this phase creates/modifies:** `wrangler.toml`, `package.json`, `src/index.ts`, `vitest.config.ts`, `README.md`.

**Tests this phase adds:** A single smoke test that the worker responds 200.

**Done-when:**
- [ ] `npm test` passes.
- [ ] `wrangler deploy` produces a public URL.
- [ ] URL is in `README.md`.

**Session budget:** ~1 session.

---

### Phase 1 — _One-line title_

**Goal:** _The single user-visible outcome of this phase._

**Context to load:** _Which sections of PRD.md, DESIGN.md, and which existing files. Be specific — "PRD §4 user story 1" beats "the PRD"._

**Files this phase creates/modifies:**
- _path/to/file.ts — what changes_
- _path/to/test.ts — what's tested_

**Tests this phase adds:** _Names of tests, one line each._

**Done-when:**
- [ ] _A user can do X._
- [ ] _Tests for X pass._
- [ ] _Diagram in `docs/architecture.md` reflects the new state._

**Session budget:** _< 1, 1, 1–2, 2+ sessions._

**Risks / unknowns:** _Anything Claude should flag if it surfaces._

---

### Phase 2 — _One-line title_

_(same structure as Phase 1)_

---

### Phase 3 — _One-line title_

_(same structure)_

---

## Decision log

A short append-only log of when the plan changed and why. Helps future-you understand why the current phase numbering exists.

| Date | Phase touched | Change | Reason |
|---|---|---|---|
| YYYY-MM-DD | — | Initial plan | — |

---

## Handoff notes

_What state should the repo be in when this plan is "done"? Used to verify the project shipped._

- Public URL deployed and linked from README.
- All Must-have user stories from PRD.md §4 have green tests.
- Architecture diagram regenerated and committed.
- Demo video and PRD video linked from README.
