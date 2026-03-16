---
name: deep-spec
description: Research-powered discovery session. Does web and codebase research first, then runs an informed interrogation that eliminates assumptions before building. Like /spec but with homework done upfront. Use when someone says "deep spec", "research and spec", "spec with research", "investigate and spec", or "/deep-spec".
---

# Deep Spec — Research-Informed Discovery

Like `/spec`, but with homework. Researches the web and codebase **first**, then uses those findings to ask sharper, more informed questions during discovery.

## Philosophy

> "The best questions come from someone who already understands the landscape."

A pure interrogation only knows what the user tells it. Deep Spec arrives prepared — it knows what competitors are doing, what exists in the codebase already, and what patterns are in play. Then it asks questions that cut deeper.

## Contract

| | |
|---|---|
| **Input** | A feature idea + access to the codebase it will live in |
| **Output** | Research Brief + Decision Log with architecture direction |
| **Time** | 15-25 minutes (research adds ~10 min) |
| **Stop condition** | All phases complete, or user says "that's enough" |

---

## Phase 0: Initial Briefing (Before Research)

Before researching, get just enough context to know what to look for.

Use `AskUserQuestion` with 2-3 questions:

- **What are we building?** (One sentence — just enough to aim the research)
- **What area of the codebase is this likely to touch?** (A repo, a directory, "the API layer", "frontend", etc.)
- **Any specific competitors, prior art, or references I should look at?** (URLs, product names, or "none")

**Do NOT go deep here.** This is just targeting information for the research sweep. Save the real interrogation for the waves.

---

## Phase 1: Research Sweep

Run these in parallel using the `Task` tool with subagents. Do not ask the user to wait — just do the work.

### 1a. Web Research

Use `WebSearch` and `WebFetch` to find:

- **Competitors & prior art** — How do others solve this problem? What's the dominant pattern?
- **Relevant documentation** — Official docs for any APIs, libraries, or protocols involved
- **Common pitfalls** — Blog posts, Stack Overflow threads, or postmortems about building this type of feature
- **Industry standards** — Are there established patterns, RFCs, or best practices?

Compile findings into a brief (5-10 bullet points max). Focus on:
- What's been tried before and how it went
- Standard approaches vs. novel ones
- Known gotchas or failure modes

### 1b. Codebase Research

Use `Glob`, `Grep`, and `Read` (via an Explore subagent) to find:

- **Existing related code** — Is there something similar already built? Partially built? Abandoned?
- **Architecture patterns** — What patterns does the codebase use? (API structure, state management, component conventions)
- **Relevant dependencies** — What libraries/frameworks are already in play that relate to this feature?
- **Integration points** — Where would this feature plug in? What existing code would it touch?
- **Test patterns** — How are similar features tested?

Compile findings into a brief (5-10 bullet points max). Focus on:
- What already exists that we should reuse or extend
- What patterns we should follow for consistency
- What constraints the existing architecture imposes

### 1c. Research Summary

After both research tracks complete, synthesize into a **Research Brief**:

```markdown
## Research Brief: [Feature Name]

### What Exists in the Codebase
- [Finding 1]
- [Finding 2]

### What the Industry Does
- [Finding 1]
- [Finding 2]

### Key Constraints Discovered
- [Constraint 1]
- [Constraint 2]

### Open Questions Raised by Research
- [Question the research surfaced that the user needs to answer]
```

**Present this brief to the user** before starting the question waves. Say: "Here's what I found. Let me know if anything here is surprising or wrong, then I'll start asking questions."

---

## Phase 2: Informed Question Waves

5-wave structure, where each wave is **informed by the research brief**. Reference specific findings when asking questions.

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

**Research-informed additions:**
- "I found [competitor X] solves this by [approach]. Is that the direction you're thinking, or something different?"
- "The codebase already has [related feature]. How does what you want relate to that?"
- "You said the workaround is X, but I noticed the codebase already has Y — did you know about that?"

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

**Research-informed additions:**
- "Based on the codebase, the simplest path seems to be [extending X]. Does that match your vision, or do you want something standalone?"
- "I see two paths: extend the existing [X] system, or build a new [Y]. The tradeoff is..."

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

**Research-informed additions:**
- "I found blog posts about [common pitfall]. Here's how it works: [plain language]. Should we plan for this?"
- "The codebase uses [library X] for similar features. It has [known limitation]. Does that affect us?"

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

**Research-informed additions:**
- "This feature would need to integrate with [existing system X] — here's how that currently works: [brief explanation]."
- Present the dependency graph you discovered from codebase research. Don't just ask — show what you found.

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
# Deep Spec Discovery: [Feature Name]
Date: [today]

## Research Brief
### Codebase Findings
- [Key finding 1]
- [Key finding 2]

### Industry/Web Findings
- [Key finding 1]
- [Key finding 2]

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

## Architecture Direction
**Approach:** [Chosen approach based on research + discussion]
**Existing code to extend:** [Files/modules identified]
**New code needed:** [What needs to be built fresh]
**Key patterns to follow:** [Patterns from codebase research]

## Key Risks Identified
- [Risk 1]: [Mitigation or "needs investigation"]
- [Risk from research]: [What was found + how to handle]

## Technical Concepts Discussed
- [Term]: [Plain language summary for reference]

## Next Step
→ Ready to generate a PRD or start implementation planning
→ OR: needs more investigation on [open items]
```

---

## Interaction Style

- **Be relentlessly curious** — ask follow-ups on every answer
- **Reference your research** — "I found X, which is why I'm asking..."
- **Challenge vague answers** — "Can you give me a specific example?"
- **Play devil's advocate** — "What if a user does [unexpected thing]?"
- **Present options, not just questions** — "Based on research, I see paths A, B, and C..."
- **Celebrate good answers** — "That's a really clear scope boundary"
- **Never assume** — if something seems obvious, ask anyway
- **Pace the questions** — 2-4 per round using AskUserQuestion, not a wall of text
- **Summarize after each wave** — "Here's what I've captured so far..."
- **Allow early exit** — if the user says "that's enough" or "let's just build it", respect that and compile what you have

---

## Trigger Phrases

- "/deep-spec"
- "deep spec"
- "research and spec"
- "spec with research"
- "investigate and spec"
- "deep discovery"
- "research this feature"

---

## When to Use /deep-spec vs /spec

| Aspect | /spec | /deep-spec |
|--------|-------|------------|
| Research | None — purely Socratic | Web + codebase research first |
| Questions | Abstract, exploratory | Informed, reference-specific findings |
| Risk discovery | User-reported | Research-surfaced + user-reported |
| Architecture | Discussed abstractly | Proposed based on codebase analysis |
| Time | Fast (5-10 min) | Longer (15-25 min with research) |
| Best for | Greenfield ideas, early brainstorms | Features in existing codebases, complex integrations |
