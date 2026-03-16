# /spec and /deep-spec — Claude Code Skills for Feature Discovery

Two skills that turn Claude Code into a product discovery partner. They ask the right questions before any code gets written, so you build the right thing the first time.

## What Are Claude Code Skills?

[Claude Code](https://docs.anthropic.com/en/docs/claude-code) is Anthropic's CLI tool that lets Claude work directly in your terminal. **Skills** are reusable prompt files that teach Claude specific workflows. When you type `/spec` in Claude Code, it loads the skill and follows its instructions.

Think of skills like runbooks for Claude — instead of explaining your process every time, you write it once and invoke it with a slash command.

## The Skills

### `/spec` — Lightweight Discovery

A Socratic interrogation in 5 waves. No research, no codebase scanning — just sharp questions that surface assumptions.

**Best for:** Greenfield ideas, early brainstorms, quick scope checks.

**Time:** 5-15 minutes.

**Waves:**
1. **The Problem** — What are we solving? For whom? What are the stakes?
2. **The Shape** — MVP scope, non-goals, user journey, success metrics
3. **Edge Cases & Risk** — Error states, scale, security, reversibility
4. **Dependencies** — Prerequisites, third-party services, incremental delivery
5. **Gut Check** — 2-sentence summary, riskiest assumption, confidence score

**Output:** A Decision Log with scope, decisions, open questions, and risks. ([See example](examples/decision-log.md))

---

### `/deep-spec` — Research-Powered Discovery

Same 5 waves, but Claude does homework first. It scans your codebase for existing patterns and searches the web for competitors, prior art, and common pitfalls — then uses those findings to ask sharper questions.

**Best for:** Features in existing codebases, complex integrations, anything touching third-party APIs.

**Time:** 15-25 minutes.

**Additional phases:**
- **Phase 0:** Quick briefing (what to build, where in the codebase, any references)
- **Phase 1:** Parallel web + codebase research, producing a Research Brief
- **Phase 2:** The same 5 waves, but informed by research findings

**Output:** A Research Brief + Decision Log with architecture direction. ([See example](examples/research-brief.md))

---

### When to Use Which

| Aspect | /spec | /deep-spec |
|--------|-------|------------|
| Research | None — purely Socratic | Web + codebase research first |
| Questions | Abstract, exploratory | Informed by specific findings |
| Risk discovery | User-reported only | Research-surfaced + user-reported |
| Architecture | Discussed abstractly | Proposed based on codebase analysis |
| Time | 5-15 min | 15-25 min |
| Best for | Greenfield ideas, brainstorms | Existing codebases, integrations |

## Installation

### Quick Start

Copy the skill folders into your Claude Code skills directory:

```bash
# Clone the repo
git clone https://github.com/bflynn4141/claude-spec-skills.git

# Copy skills to your Claude Code config
cp -r claude-spec-skills/skills/spec ~/.claude/skills/spec
cp -r claude-spec-skills/skills/deep-spec ~/.claude/skills/deep-spec
```

### Manual Install

If you prefer, just create the files directly:

```
~/.claude/skills/
  spec/
    skill.md          # Copy from skills/spec/skill.md
  deep-spec/
    skill.md          # Copy from skills/deep-spec/skill.md
```

### Verify

Open Claude Code and type `/spec` — you should see it in the skill list.

## Usage

```
# Start a lightweight discovery session
/spec

# Start a research-powered discovery session
/deep-spec

# You can also trigger them with natural language:
"let's spec this"
"help me think through this feature"
"deep spec the auth system"
"research and spec"
```

Claude will enter plan mode, ask you questions in waves, and produce a structured Decision Log at the end.

## Customization

These skills are designed to be forked and tweaked. Common modifications:

- **Add/remove questions** — Edit the wave sections to match your team's concerns
- **Change the output template** — Modify the Decision Log format to match your team's PRD structure
- **Adjust pacing** — Change "2-4 questions per round" to batch differently
- **Add a pipeline** — The "Next Step" section is intentionally generic. Add your own follow-up skills (PRD generator, evaluator, etc.)
- **Tune the tone** — The "Interaction Style" section controls how aggressive the questioning is

## How It Works Under the Hood

Both skills use built-in Claude Code tools:

- **`EnterPlanMode`** — Prevents code changes during discovery (you're defining scope, not building yet)
- **`AskUserQuestion`** — Presents questions with clickable options in the terminal
- **`Task` with subagents** — `/deep-spec` runs web and codebase research in parallel background agents
- **`WebSearch` / `WebFetch`** — For competitor and prior art research
- **`Glob` / `Grep` / `Read`** — For codebase scanning

No external dependencies. No API keys. Just Claude Code.

## Examples

- [Decision Log](examples/decision-log.md) — Sample output from `/spec`
- [Research Brief + Decision Log](examples/research-brief.md) — Sample output from `/deep-spec`

## License

MIT — use these however you want.
