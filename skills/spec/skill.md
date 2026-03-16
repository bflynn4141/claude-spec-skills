---
name: spec
description: Interactive discovery session that eliminates assumptions before building. Enters plan mode and asks exhaustive questions from both product and technical perspectives, explained for non-technical stakeholders. Use when someone says "spec", "let's spec this", "what should we build", "help me think through", "define requirements", "scope this out", or "/spec".
---

# Spec — Discovery & Requirements Interrogation

The discovery phase before any code gets written. Enters plan mode and asks **every question that matters** — from both the product ("what and why") and technical ("how and what could go wrong") perspectives — explained in plain language.

## Philosophy

> "Every assumption you don't surface now becomes a bug, a delay, or a feature nobody wanted."

This is an **interactive interrogation** that ensures everyone agrees on what "done" looks like before a single line of code is written.

## Contract

| | |
|---|---|
| **Input** | A feature idea, problem statement, or "I want to build X" |
| **Output** | A Decision Log with scope, decisions, open questions, and risks |
| **Time** | 5-15 minutes depending on complexity |
| **Stop condition** | All 5 waves complete, or user says "that's enough" |

---

## How It Works

1. **Enter plan mode** immediately via `EnterPlanMode` (plan mode prevents code changes while you're still defining what to build)
2. **Ask questions in waves** — don't dump 30 questions at once
3. **Explain every technical concept** in plain language with analogies
4. **Surface hidden assumptions** the user didn't know they were making
5. **Produce a decision log** — what was decided, what's still open

---

## Question Waves

### Wave 1: The Problem (Product Perspective)

Start here. Don't let anyone jump to solutions.

Use `AskUserQuestion` for each wave, batching 2-4 questions per round.

**Questions to cover:**
- What problem are we solving? Who has this problem?
- How do they deal with it today? (What's the workaround?)
- What happens if we don't build this? (Stakes check)
- Who is the primary user? Is there a secondary user?
- What does success look like from the user's point of view?
- Is there a specific trigger — why now?

**Explain like:** "Before we talk about *how* to build it, I want to make sure we agree on *what* we're solving and *for whom*. The #1 cause of wasted engineering time is building the right solution to the wrong problem."

### Wave 2: The Shape (Product + Light Technical)

Now scope the solution — still mostly product, but start introducing constraints.

**Questions to cover:**
- What's the simplest version that delivers value? (MVP)
- What are you explicitly NOT building? (Non-goals)
- Walk me through the user's journey step by step — what do they see, click, experience?
- Are there existing patterns in the product we should match?
- What data does the user need to provide? What do they get back?
- How will you know it's working? (Success metrics)
- Is there a deadline or external constraint?

**Explain like:** "Think of this like drawing the blueprint of a house — we're deciding how many rooms, not picking paint colors. The 'not building' list is just as important as the 'building' list because it prevents scope creep (where a feature keeps growing until it's late and bloated)."

### Wave 3: Edge Cases & Risk (Technical, Explained Simply)

This is where engineering landmines hide. Surface them in plain language.

**Questions to cover:**
- What happens when things go wrong? (Error states)
  - *"If a user loses internet halfway through, what should happen?"*
  - *"If the data they enter is invalid, how should we respond?"*
- What are the scale expectations?
  - *"Are we talking 10 users or 10,000? This changes the architecture — like the difference between a food truck and a restaurant."*
- Does this touch money, authentication, or sensitive data?
  - *"These areas need extra care — like how a bank has more security than a library."*
- What existing systems does this connect to?
  - *"Every connection to another system is a potential point of failure — like adding another link to a chain."*
- Are there permissions or access control concerns?
  - *"Who should be able to do this? Who should NOT?"*
- Is this reversible? Can users undo it?

**Explain like:** "Engineers think about what happens when things break, not just when they work. I'm going to ask some 'what if' questions that might seem paranoid, but each one represents a real scenario that WILL happen eventually."

### Wave 4: Dependencies & Sequencing (Technical, Explained Simply)

Uncover hidden prerequisites and ordering constraints.

**Questions to cover:**
- Does anything need to exist before we can build this?
  - *"Like how you need a foundation before walls — are there technical prerequisites?"*
- Can this be built independently or does it block/depend on other work?
- Are there third-party services or APIs involved?
  - *"Every external service is a dependency we don't control — like relying on a supplier."*
- Is there existing code we're modifying vs. building from scratch?
- Can we ship this incrementally or is it all-or-nothing?
  - *"Can we deliver value in stages, or does it only work when 100% complete?"*

### Wave 5: The Gut Check (Final Validation)

Before wrapping up, pressure-test the whole picture.

**Questions to cover:**
- If you had to explain this feature to a new team member in 2 sentences, what would you say?
- What's the one thing that would make you say "this failed"?
- What's the riskiest assumption we're making?
- Is there anything we discussed that you're still unsure about?
- On a scale of 1-10, how confident are you in the scope?

---

## Explaining Technical Concepts

When a technical concept comes up, ALWAYS explain it using this pattern:

```
**[Technical Term]** — [Plain language definition]
Think of it like: [Analogy from everyday life]
Why it matters here: [Specific relevance to their feature]
```

Examples:
- **API** — A way for two systems to talk to each other. Think of it like a waiter taking your order to the kitchen and bringing food back. Why it matters: your feature needs to communicate with [X service].
- **Database migration** — Changing the structure of where data is stored. Think of it like remodeling a filing cabinet while all the files are still in it. Why it matters: we need to add a new field for [X].
- **Race condition** — When two things happen at the same time and fight over the same resource. Think of it like two people trying to edit the same Google Doc at the exact same moment. Why it matters: if two users [do X] simultaneously, we need to handle that.

---

## Output: Decision Log

After all waves, compile a decision log (written to the plan file):

```markdown
# Spec Discovery: [Feature Name]
Date: [today]

## Decisions Made
- [Decision 1]: [What was decided and why]
- [Decision 2]: [What was decided and why]

## Still Open
- [ ] [Unresolved question 1]
- [ ] [Unresolved question 2]

## Scope Summary
**Building:** [Bullet list]
**NOT Building:** [Bullet list]
**MVP vs V2:** [What's deferred]

## Key Risks Identified
- [Risk 1]: [Mitigation or "needs investigation"]

## Technical Concepts Discussed
- [Term]: [Plain language summary for reference]

## Next Step
→ Ready to generate a PRD or start implementation planning
→ OR: needs more investigation on [open items]
```

---

## Interaction Style

- **Be relentlessly curious** — ask follow-ups on every answer
- **Challenge vague answers** — "Can you give me a specific example?"
- **Play devil's advocate** — "What if a user does [unexpected thing]?"
- **Celebrate good answers** — "That's a really clear scope boundary"
- **Never assume** — if something seems obvious, ask anyway
- **Pace the questions** — 2-4 per round using AskUserQuestion, not a wall of text
- **Summarize after each wave** — "Here's what I've captured so far..."
- **Allow early exit** — if the user says "that's enough" or "let's just build it", respect that and compile what you have

---

## Trigger Phrases

- "/spec"
- "spec this"
- "let's spec this"
- "help me think through"
- "what should we build"
- "define requirements"
- "scope this out"
- "let's define this feature"
- "walk me through what we need"
