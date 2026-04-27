# Product Requirements Document (PRD)

_Fill this out by having a conversation with Claude. Don't try to write it alone on a blank page — that's what the AI is for. Use the `/skills/pm-interview` skill to drive the conversation._

> **Status:** Draft / In Review / Locked
> **Last updated:** YYYY-MM-DD
> **Author:** Your name
> **Stakeholder:** Yourself / Name of the business or person you're building for

---

## 1. The problem

_In one paragraph, what is the problem? Who has it? What do they do today without your tool? Why is the status quo bad enough that someone would switch?_

**Bad example:** "People need a way to track tasks."
**Good example:** "Maria, who owns Maria's Tacos, posts weekly specials on Instagram, Facebook, and a chalkboard out front. She updates each one manually on Sunday nights and forgets one about half the time, so customers show up expecting Tuesday's special on Wednesday. She'd pay $10/month to update once and have all three update automatically."

---

## 2. The user

Who specifically will use this? Be concrete. If you can't name a real person (or a realistic archetype), go talk to people until you can.

- **Primary user:**
- **Their current workflow:**
- **Their technical comfort:** _(Are they comfortable with a web app? A chat interface? Do they live in their email?)_
- **What device will they use it on?** _(Phone, desktop, both?)_

---

## 3. What success looks like

How will you know this worked? Pick metrics you can actually measure.

- **Must-have outcome:** _e.g. "Maria can update all three channels from one form in under 60 seconds."_
- **Nice-to-have outcome:** _e.g. "Maria sees how many people clicked through from each channel."_
- **Not a goal:** _e.g. "This is not a full CRM. We don't care about customer data."_

---

## 4. Core user stories

List the things a user does with this product, in order of importance. Start with the single most important one. A good user story is "_As a [user], I want to [do thing] so that [outcome]._"

1. **[Must]** As a _______, I want to _______ so that _______.
2. **[Must]** ...
3. **[Should]** ...
4. **[Could]** ...
5. **[Won't — this release]** ...

_Use MoSCoW (Must / Should / Could / Won't) to force yourself to prioritize. If everything is a Must, nothing is._

---

## 5. Out of scope

_What are you explicitly NOT building? This is the most important section. Everything not listed as in-scope above is at risk of scope creep — name the things you've been tempted by and are choosing not to build._

- ...
- ...

---

## 6. Technical shape

_This is where Claude can help most. Describe the shape, not the implementation. You'll refine this in code._

- **Type of app:** _(Static site? Full-stack web app? API only?)_
- **Does it need to store data?** _(If yes, what kind — structured records, files, both?)_
- **Does it need authentication?** _(If yes, who can log in — you, your users, both?)_
- **Does it need to call external services?** _(e.g. Instagram API, email sending, AI model)_
- **Who pays for hosting?** _(This matters — Cloudflare's free tier is generous but not infinite.)_

### Proposed Cloudflare stack

_Fill this in after discussing with Claude. Ask Claude to justify each choice._

| Need | CF Product | Why |
|---|---|---|
| Hosting the web UI | | |
| Backend logic | | |
| Structured data | | |
| File storage | | |
| AI features | | |

---

## 7. Risks and unknowns

What could go wrong or surprise you? Be honest.

- **Biggest risk:**
- **Things I don't know how to do yet:**
- **Things I'm assuming but haven't verified:**

---

## 8. Milestones

A realistic plan for the next 3 weeks of building.

- **Week 2 end:** _What's deployed and working?_
- **Week 3 end:** _What's deployed and working?_
- **Week 4 demo:** _What does the final walkthrough show?_
