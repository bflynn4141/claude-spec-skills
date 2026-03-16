# discovery — Claude Code Plugin for Feature Discovery

A Claude Code plugin with two skills that turn Claude into a product discovery partner. They ask the right questions before any code gets written, so you build the right thing the first time.

## Skills

| Skill | What it does | Time |
|-------|-------------|------|
| `/discovery:spec` | Socratic 5-wave interrogation — sharp questions, no research | 5-15 min |
| `/discovery:deep-spec` | Research-powered discovery — scans codebase + web first, then asks informed questions | 15-25 min |

## Install

### From GitHub (recommended)

Clone the repo and point Claude Code at it:

```bash
git clone https://github.com/bflynn4141/claude-spec-skills.git ~/claude-plugins/discovery
```

Then in Claude Code:

```
/plugin install ~/claude-plugins/discovery
```

### Local development

Test without installing:

```bash
claude --plugin-dir ./claude-spec-skills
```

## Usage

```
/discovery:spec                    # Start lightweight discovery
/discovery:deep-spec               # Start research-powered discovery
```

Both skills also trigger on natural language:

- "let's spec this", "help me think through", "define requirements"
- "deep spec", "research and spec", "investigate and spec"

## How /discovery:spec Works

A Socratic interrogation in 5 waves that progressively moves from product to technical questions:

```
Wave 1: The Problem       → What are we solving? For whom? Stakes?
Wave 2: The Shape          → MVP, non-goals, user journey, success metrics
Wave 3: Edge Cases & Risk  → Errors, scale, security, reversibility
Wave 4: Dependencies       → Prerequisites, third-party APIs, incremental delivery
Wave 5: Gut Check          → 2-sentence summary, riskiest assumption, confidence
```

**Output:** A structured Decision Log. ([See example](examples/decision-log.md))

## How /discovery:deep-spec Works

Same 5 waves, but Claude does homework first:

```
Phase 0: Briefing          → What to build, where in codebase, any references
Phase 1: Research Sweep    → Parallel web + codebase scan (competitors, patterns, pitfalls)
Phase 2: Informed Waves    → Same 5 waves, but referencing specific findings
```

**Output:** A Research Brief + Decision Log with architecture direction. ([See example](examples/research-brief.md))

## When to Use Which

| | /discovery:spec | /discovery:deep-spec |
|---|---|---|
| **Research** | None — purely Socratic | Web + codebase research first |
| **Questions** | Abstract, exploratory | Informed by specific findings |
| **Risk discovery** | User-reported only | Research-surfaced + user-reported |
| **Architecture** | Discussed abstractly | Proposed based on codebase analysis |
| **Best for** | Greenfield ideas, brainstorms | Existing codebases, integrations |

## Examples

- [Decision Log](examples/decision-log.md) — Sample output from `/discovery:spec`
- [Research Brief + Decision Log](examples/research-brief.md) — Sample output from `/discovery:deep-spec`

## Customization

Fork and tweak. Common modifications:

- **Add/remove questions** in the wave sections
- **Change the Decision Log template** to match your team's PRD format
- **Adjust question pacing** (default: 2-4 per round)
- **Tune the tone** via the Interaction Style section
- **Add a pipeline** — the "Next Step" is intentionally generic

## How It Works

Both skills use built-in Claude Code tools. No external dependencies, no API keys.

| Tool | Used by | Purpose |
|------|---------|---------|
| `EnterPlanMode` | Both | Prevents code changes during discovery |
| `AskUserQuestion` | Both | Presents questions with clickable options |
| `Task` (subagents) | deep-spec | Runs web + codebase research in parallel |
| `WebSearch` / `WebFetch` | deep-spec | Competitor and prior art research |
| `Glob` / `Grep` / `Read` | deep-spec | Codebase scanning |

## Plugin Structure

```
claude-spec-skills/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest (name, version, author)
├── skills/
│   ├── spec/
│   │   └── SKILL.md         # /discovery:spec
│   └── deep-spec/
│       └── SKILL.md         # /discovery:deep-spec
├── examples/
│   ├── decision-log.md      # Sample /discovery:spec output
│   └── research-brief.md    # Sample /discovery:deep-spec output
├── README.md
└── LICENSE
```

## License

MIT
