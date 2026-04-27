---
name: explain-this-code
description: Explain code the developer doesn't understand, at the right level of depth. Use this skill when the developer asks things like "what does this do", "explain this file", "I don't understand why this works", "walk me through this", or points at a function/file/line and asks for an explanation. Also use proactively whenever you've just written non-obvious code and the developer hasn't shown they understand it.
---

# Explain This Code

Your job is to teach, not narrate. The goal is that the developer could explain the code to someone else after you're done — not just nod along.

## How to explain

### 1. Calibrate first

Before explaining, spend 30 seconds figuring out what the developer already knows. Ask one question:

- "Before I explain — what's your current guess about what this does?"

Their answer tells you where to start. If they're on the right track, skip the basics. If they're lost, start further back.

### 2. Three layers

Explain in three layers, pausing between each to check understanding:

**Layer 1: What it does (1–2 sentences).**
The thirty-thousand-foot view. "This function takes a user ID and returns their unread notifications."

**Layer 2: How it does it.**
Walk the key steps. Focus on the *decisions* the code makes — conditions, loops, async boundaries, data transformations. Skip trivial syntax.

**Layer 3: Why it's written this way.**
This is the layer most explanations skip, and it's the most important. What were the alternatives? What tradeoff did this code make? Why this library? Why this pattern?

Examples of "why" that matter:
- Why a `for...of` instead of `.map()` (side effects, early exit)
- Why `await`ing sequentially instead of `Promise.all` (rate limits, order dependency)
- Why a class instead of a function (stateful lifecycle)
- Why D1 instead of KV (queries, relationships)

### 3. Ask before moving on

After each layer, stop and ask: "Does that make sense, or want me to go slower on any part?" Don't steamroll.

### 4. Connect to what they know

If the developer mentioned a language or framework they came from (React, Python, C#), use analogies to that when useful. "This is like a Python context manager — it guarantees cleanup." But don't over-analogize; sometimes the new thing is genuinely new.

### 5. End with a check

Close by asking the developer to explain one specific part back to you. Not "do you get it?" — they'll say yes. Something concrete: "In your own words, what happens if the fetch fails on line 14?"

If they can't answer, that's the part to re-explain.

## What NOT to do

- **Don't just re-paste the code with comments.** That's not teaching, it's narrating.
- **Don't over-explain trivial parts.** If they wrote `const x = 5`, don't explain const. Focus on the parts that actually matter.
- **Don't explain bugs as if they were features.** If the code has a bug or smell, say so honestly: "This works, but it's fragile because ___."
- **Don't reassure when you shouldn't.** If the code is genuinely complex and takes real time to absorb, say that. "This takes most people a few passes to internalize" is honest and reduces shame.

## For architecture-level explanations

If the developer asks about the whole app (not a specific function):

1. Generate a Mermaid diagram showing the major components and their relationships.
2. Explain the request flow for the most common user action end-to-end.
3. Point out the 2–3 design decisions that shaped everything else.
4. Ask which part they want to go deeper on.

Don't try to explain the whole codebase at once. Depth beats breadth.
