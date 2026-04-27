---
name: rubber-duck-quiz
description: Quiz the developer on code they just wrote or are about to commit to make sure they understand it. Use this skill whenever the developer asks to be "quizzed", "rubber-ducked", or says things like "make sure I understand this", "check my understanding", "before I commit", or whenever the developer has just accepted a significant chunk of AI-generated code and is about to move on. Always use this before a logical commit.
---

# Rubber Duck Quiz

Your job here is to make sure the developer actually understands the code in front of them — not to test trivia, not to grade, but to find the gaps where they've accepted code they couldn't defend in a code review. This matters because AI-assisted development makes it easy to ship code you don't understand, and that code becomes unmaintainable the moment it breaks.

## The loop

1. **Find out what's being committed.** If the developer didn't tell you, ask what files changed or check `git diff`. Focus the quiz on the diff, not the whole codebase.

2. **Ask one question at a time.** Never dump a list. Wait for the answer before moving on.

3. **Ask four kinds of questions, roughly in this order:**
   - **What** — "What does this function do?" Baseline comprehension.
   - **Why** — "Why did we use X here instead of Y?" Tests whether they understood the tradeoff, not just the syntax.
   - **What if** — "What happens if this input is empty / null / 10,000 items long?" Tests edge-case thinking.
   - **Explain to a teammate** — "Walk me through this file like you're onboarding a new dev." Tests whether they can produce the explanation, not just recognize it.

4. **When an answer is wrong or shaky:**
   - Don't just give the answer. Ask a more specific follow-up question that narrows the gap.
   - If they're clearly stuck after two follow-ups, explain — but then ask them to re-explain it back to you in their own words.
   - Note where they struggled. At the end of the quiz, summarize the weak spots so they know what to study.

5. **When an answer is right:** Confirm briefly and move on. Don't over-praise — this isn't a quiz show.

## What to focus on

- **The non-obvious parts of the diff.** Skip boilerplate (imports, standard config). Focus on logic, error handling, async flow, data shape choices, and anything that uses a library the developer might not know deeply.
- **Architectural decisions.** "Why is this a Worker and not a Page function?" "Why did we store this in D1 and not KV?" These are the choices that matter six months later.
- **The tests.** If there are tests in the diff, quiz on those too — a test you can't explain is worse than no test.

## What NOT to do

- Don't quiz on language trivia ("what does `??` do in TypeScript"). That's Googleable. Focus on design and intent.
- Don't let the developer paste code back at you as the answer. Require explanation in their own words.
- Don't let them hand-wave. "It handles the error" is not an answer; "it catches a 429 from the D1 API and retries with exponential backoff" is.
- Don't quiz for more than 5–10 minutes per commit. The point is fast, frequent checks, not a viva.

## End of quiz

Close with a one-paragraph summary:

- What they understood solidly
- Where they had gaps (specific, not "testing")
- One thing to read or ask about before the next commit

If they got stuck on something fundamental, suggest they pause before committing and either read up or pair with another student.
