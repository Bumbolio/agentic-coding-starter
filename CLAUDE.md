# Project Context for Claude

This file tells Claude how to work on this codebase. Keep it accurate — an out-of-date CLAUDE.md is worse than no CLAUDE.md at all.

## About this project

**What it is:** _One sentence. Update this as your project evolves._

**Who it's for:** _Who is the user? Be specific. "Small business owners" is vague; "the owner of Maria's Tacos who needs to post weekly specials" is useful._

**Product requirements:** The full PRD lives in `docs/PRD.md`. Read it before making meaningful architectural or feature decisions.

**Design brief:** UI/UX decisions live in `docs/DESIGN.md`. Read it before writing UI code or making visual choices. The defaults captured there are React + Headless UI + Tailwind CSS unless the brief says otherwise.

**Build plan:** The phased build plan lives in `docs/BUILDPLAN.md`. Read it to know which phase is current, what files that phase touches, and what context to load. Each phase names exactly which PRD/DESIGN sections and files to load — honor that scope to keep sessions cheap. If you're starting work that isn't covered by the current phase, stop and ask whether the plan needs to change.

## Tech stack

- **Language:** TypeScript
- **Runtime:** Cloudflare Workers
- **Framework:** Hono (if used)
- **Database:** Cloudflare D1 (if used)
- **Storage:** Cloudflare R2 (if used)
- **Testing:** Vitest with `@cloudflare/vitest-pool-workers`
- **Deployment:** Wrangler to Cloudflare

_Remove lines above that don't apply. Add any others you add later._

## How to work on this code

### Before writing code

1. Read the relevant section of `docs/PRD.md`.
2. If the change is UI-related, also read the relevant section of `docs/DESIGN.md`. Honor the component, token, and a11y choices captured there.
3. If the change is non-trivial (more than a few lines), use plan mode (`shift+tab` to enter it) and share the plan before making edits.
4. Check if there's an existing pattern in the codebase you should follow. Don't invent new patterns when an old one works.

### While writing code

- **TDD is the default.** Write a failing test first, then make it pass, then refactor. If you skip tests, say so explicitly and why.
- Run `npm test` after every logical unit of work. Do not consider a task complete until tests pass.
- Prefer small, composable functions over large ones.
- Use TypeScript strictly — no `any` unless there's a real reason, and then comment why.

### After writing code

- Run `npm test` and confirm it passes.
- Run `npm run typecheck` if the project has one.
- Summarize what changed in 1–3 sentences. This summary becomes the commit message.

### What NOT to do

- Don't add dependencies without asking. If a library would help, propose it and wait.
- Don't delete tests to make them pass. Fix the code or update the test intentionally.
- Don't commit secrets. Use `wrangler secret` for production values and `.dev.vars` (gitignored) for local.
- Don't use `--dangerously-skip-permissions` style flags silently. If autonomy increases, tell the human.

## Cloudflare-specific notes

- Use `wrangler dev` for local development. It runs the real Workers runtime (miniflare), not Node.
- Environment bindings (D1, R2, KV, Workers AI) are declared in `wrangler.toml` and accessed via `env.BINDING_NAME`, not `process.env`.
- For Cloudflare docs, two LLM-optimized entry points exist:
  - `https://developers.cloudflare.com/llms.txt` — the **index**. Lightweight; fetch this first to see what products and pages exist and to find the right deeper link.
  - `https://developers.cloudflare.com/llms-full.txt` — the full corpus. Heavy; only fetch this (or a specific page from the index) when you need real product details.
  - Rule of thumb: when a Cloudflare question comes up, fetch `llms.txt` first, locate the right section, then fetch only that section. Don't guess from training data when current docs are one fetch away.
- Workers have limits (CPU time, size, subrequests). If a design pushes against these, flag it.

## Teach-back mode

This is a learning project. When the human asks "explain this" or "quiz me on this," switch into teaching mode:

- Don't just describe what the code does — explain *why* it's written this way and what the alternatives were.
- When quizzing, ask one question at a time. Wait for an answer. If the answer is wrong or partial, help them get to the right answer through questions, not by telling them.
- If the human can't explain a piece of code, that's a signal we moved too fast. Slow down, refactor for clarity, or add comments.

## Project-specific rules

_Add rules here as you discover them. Examples:_

- _"All API routes are under `/api/v1/`. Don't add routes outside that prefix."_
- _"User-facing text is in `src/strings.ts`. Don't hardcode copy in components."_
- _"We use ULIDs, not UUIDs, for all IDs."_
