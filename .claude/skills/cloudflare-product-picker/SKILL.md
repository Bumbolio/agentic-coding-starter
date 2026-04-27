---
name: cloudflare-product-picker
description: Recommend the right Cloudflare product for a specific need, and justify the choice. Use this skill when the developer asks things like "should I use D1 or KV", "where should I store this", "what's the right Cloudflare product for X", "how do I host my app on Cloudflare", or describes a feature they need to build and asks how to architect it. Also use proactively whenever a new feature is being planned that requires storage, compute, or AI.
---

# Cloudflare Product Picker

Your job is to match a specific technical need to the right Cloudflare product and explain *why* — not just name the product. The developer should come away understanding the tradeoff, not just the answer.

## Step 1: Get the real need

Don't just answer from the first sentence. Ask:

- What data or behavior is involved? (Records? Files? AI inference? Scheduled work?)
- How often is it read vs. written?
- How much of it is there, and how large?
- Does it need to be queryable, or just stored and retrieved by key?
- Does the user need to see it immediately (tight latency) or eventually (async OK)?

A 30-second clarification beats a confidently wrong answer.

## Step 2: Match against the map

Use this decision tree. Prefer the simplest product that fits — don't over-architect.

### "I need to serve a website"

- **Static HTML / SPA / framework output (Next, SvelteKit, Astro, React, Vue):** Cloudflare Pages. Connect to git, deploys on push.
- **Dynamic HTML rendered per request:** Workers (with a framework like Hono, or SSR via Pages Functions).
- **Just HTML/CSS/JS by hand:** Pages. Drag and drop or git.

### "I need a backend / API"

- **Default answer:** Workers. TypeScript/JS, deploys globally, scales to zero.
- **Tightly coupled to a Pages site:** Pages Functions (Workers-under-Pages) is fine for small APIs.
- **Need long-lived state per object (chat rooms, game sessions, per-user coordinators):** Durable Objects.

### "I need to store structured data"

- **Relational data, queries, joins, reports:** D1 (SQLite-based). Good default for most apps.
- **Simple key/value lookups at edge, eventually consistent, fast reads:** KV. Great for config, feature flags, cached responses. Not great when you need strong consistency or complex queries.
- **Strongly consistent state tied to a specific entity (a room, a user session, a game):** Durable Objects with storage.
- **Large analytical/time-series data:** Workers Analytics Engine.

**Common mistake:** using KV for user data that needs updates. KV is eventually consistent and writes are slow — use D1.

### "I need to store files"

- **User uploads, images, PDFs, any blob:** R2. S3-compatible, zero egress fees.
- **Tiny files that need millisecond reads:** KV works for up to 25MB but is designed for hot cache, not bulk storage.

### "I need AI"

- **Run a model at the edge:** Workers AI. Llama, Whisper, image models, embeddings. Pay per use.
- **Use an external model (OpenAI, Anthropic):** Fetch from a Worker. Store API keys as secrets.
- **Vector search:** Vectorize. Pairs well with Workers AI embeddings for RAG.
- **Proxy/observe/ratelimit AI calls:** AI Gateway.

### "I need to do work on a schedule / in the background"

- **Cron-style periodic jobs:** Workers scheduled handler (`scheduled(event, env, ctx)`).
- **Queue of tasks to process asynchronously:** Queues. Producer Worker writes, consumer Worker drains.
- **Long-running (>30s CPU) workflows with retries/steps:** Workflows.

### "I need auth"

- Cloudflare doesn't ship a full auth product for app users. Use an external provider (Clerk, Auth0, Supabase Auth, WorkOS, or roll your own with hashed passwords + sessions in D1). Cloudflare Access is for protecting internal tools, not building consumer auth.

### "I need realtime / websockets"

- Durable Objects with WebSocket handlers. Each DO instance is a single coordinator — perfect for chat rooms, collaborative docs, game state.

## Step 3: Justify in writing

Once you've picked, write the justification in this shape:

> **Recommendation:** [Product]
> **Why this fits:** [1–2 sentences tying the product's strengths to the need]
> **What we considered:** [One alternative and why it's worse for this case]
> **Watch out for:** [One real limitation, e.g. "D1 is a single region, so cross-region writes have latency"]

Put this in the PRD under the tech-shape section, or in the commit message if it's an in-flight decision.

## Step 4: Check freshness

Cloudflare ships fast. If the decision is significant (architectural, not a small feature), fetch the current docs page from `https://developers.cloudflare.com/llms-full.txt` or the specific product page before finalizing. Things that moved recently: Workers AI model availability, D1 read replicas, Workflows GA status.

## Step 5: Don't over-engineer

Most student projects fit in: **Pages + Workers + D1**, maybe plus R2 for uploads and Workers AI for one feature. Resist the urge to reach for Durable Objects, Queues, or Workflows unless the project genuinely needs them. Simpler stacks ship faster and fail less.
