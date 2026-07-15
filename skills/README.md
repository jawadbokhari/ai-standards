# Skills

Reusable Claude Code skills shared across Expertflow projects, distributed through this repo's
Claude Code plugin (see [`.claude-plugin/plugin.json`](../.claude-plugin/plugin.json)).

Claude Code auto-discovers any `skills/<skill-name>/SKILL.md` in a plugin — no extra registration
needed in `plugin.json`.

## Adding a skill

```
skills/
└── <skill-name>/
    └── SKILL.md
```

`SKILL.md` needs YAML frontmatter with `name` and `description`, followed by the skill instructions:

```markdown
---
name: <skill-name>
description: One line — what it does and when Claude should use it (used for skill discovery).
---

Skill instructions go here.
```

Keep skills stack-agnostic where possible (java/angular/node standards already live under
`java/`, `angular/`, `node/`) — this folder is for cross-cutting workflows (e.g. PR review,
service scaffolding, release checklists) rather than per-stack coding rules.

## Consuming repos

Repos that pull this repo in via `git subtree` (see [`AGENTS.md`](../AGENTS.md)) or the Claude
Code plugin marketplace automatically get skills placed here — no per-repo copy step required.
