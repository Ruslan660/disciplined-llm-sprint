# disciplined-llm-sprint Plugin

A Claude Code plugin that wraps the `disciplined-llm-sprint` skill — a 7-phase orchestrated workflow for LLM-assisted software development.

See `skills/disciplined-llm-sprint/SKILL.md` for the full skill specification.

## Components

- **Skill:** `disciplined-llm-sprint` (1 skill, 3 templates, 2 references)
- **Agents:** none
- **Hooks:** none
- **MCP:** none
- **Settings:** none

Pure process orchestration. Composes `superpowers:brainstorming`, `superpowers:writing-plans`, `superpowers:executing-plans`, `superpowers:finishing-a-development-branch` and adds discipline-specific layers (scope gate, lectures, hands-on, Block Recap, retro).

## After install

The skill is invoked via `/disciplined-llm-sprint`. The frontmatter description also enables natural-language auto-discovery when the user describes work as "new sprint / new feature / new milestone / new module".

## File layout

```
plugins/disciplined-llm-sprint/
├── .claude-plugin/plugin.json
├── README.md
└── skills/
    └── disciplined-llm-sprint/
        ├── SKILL.md
        ├── templates/
        │   ├── session-note.md
        │   ├── adr.md
        │   └── lecture-skeleton.md
        └── references/
            ├── 5-principles.md
            └── block-recap-rule.md
```
