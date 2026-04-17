# routine-writer

An [Agent Skill](https://agentskills.io) that teaches agents to write **routines** — scheduled or triggered prompts that iterate over a set of items (inbox, PRs, issues, CI, alerts), optionally take action on them, and return a short structured report. Use for briefings, triage passes, health checks, review digests, trackers, and fixers.

## Why

Without guidance, agents draft routines as long playbooks — markdown section headers, fenced tool-invocation blocks, step-by-step guardrails. The canonical routines are the opposite: terse, declarative, verb-first, with explicit empty-state handling and no implementation details. This skill codifies that shape so the routines you write slot in alongside them.

In a 3-prompt benchmark scored against 10 structural assertions, outputs passed **97%** with the skill vs. **40%** without.

## Install

```bash
npx skills add https://github.com/frontboat/routine-writer --skill routine-writer
```

Works in any harness that follows the [Agent Skills spec](https://agentskills.io/specification).

## Usage

Ask your agent for a routine and the skill triggers. For the full pattern it teaches — the three parts (title, tagline, body), the six body moves, and the common failure modes — read [SKILL.md](skills/routine-writer/SKILL.md).

## License

MIT.
