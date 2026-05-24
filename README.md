# disciplined-llm-sprint — Claude Code Plugin Marketplace

A single-plugin Claude Code marketplace hosting the `disciplined-llm-sprint` skill — a 7-phase orchestrated workflow for LLM-assisted software development.

## What's inside

| Plugin | Version | Purpose |
|--------|---------|---------|
| `disciplined-llm-sprint` | 0.3.0 | 7-phase sprint orchestrator: kickoff → planning → lectures → TDD → verification → code review → knowledge base → retro + merge |

The plugin contains one skill (`disciplined-llm-sprint`) and supporting templates + references. No agents, no hooks, no MCP servers. Pure process orchestration.

## Install

```bash
# Add this marketplace
/plugin marketplace add Ruslan660/disciplined-llm-sprint

# Install the plugin
/plugin install disciplined-llm-sprint
```

After install, the skill is invoked via `/disciplined-llm-sprint`.

## Trigger

Invoke explicitly when starting a substantial new feature, sprint, milestone, or module. The skill skips for one-off fixes — use `/gsd-quick` or `/gsd-debug` for those.

## What the skill does after invocation

Walks through 7 phases with explicit gates:

| # | Phase | Output |
|---|-------|--------|
| 0 | Sprint Kickoff | Locked scope agreement |
| 1 | Planning | Design spec + implementation plan (committed) |
| 2 | New-concept lectures | User learns each new concept before it appears in code |
| 3 | TDD Execution | Atomic RED → GREEN → REFACTOR commits per Block; mandatory recap + hands-on after each Block |
| 4 | Manual verification | UI smoke / integration test passing |
| 5 | Code review walkthrough | "If I don't understand it, I don't merge it" gate — every commit explainable by the user |
| 6 | Knowledge-base update (optional) | Concept pages, question pages, session note |
| 7 | Retrospective + merge | 3 retro questions + `--no-ff` merge |

Each phase has a blocking gate; the skill will not advance until the user confirms.

## What the skill does NOT do

- Bug fixing → use `/gsd-quick` or `/gsd-debug`.
- Refactoring with no new concepts.
- Hot fixes / urgent deploys.
- Unattended scripted batch work.

## Plugin license / sharing

Released under the MIT License — see [LICENSE](LICENSE) for the full text. Free to use, modify, fork, and redistribute, including for commercial purposes, provided the copyright notice and license terms are preserved. Install via the `/plugin marketplace add` workflow above.

## Source of truth

The plugin packages a copy of the underlying skill. Updates land here first, in the plugin repo. Bump `marketplace.json` and `plugin.json` versions on releases.
