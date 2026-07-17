# AGENTS.md

> Cross-tool coding standard for AI agents at Expertflow — works with Claude Code, Cursor, GitHub Copilot, Cline, Gemini CLI, and Codex CLI.
> Converted from [`ef-coding-standards`](https://gitlab.expertflow.com/general/ef-coding-standards), which generated Cursor-only `.mdc` rules via shell scripts. This repo replaces that generation step with a single source of truth each tool reads directly.

---

## Repository Structure

```
ai-standards/
├── AGENTS.md                             # cross-tool universal standard (this file)
├── CLAUDE.md                             # Claude Code bridge — small pointer file, tells Claude to read AGENTS.md
│
├── .claude-plugin/
│   └── plugin.json                       # makes this repo a Claude plugin
│
├── skills/
│   └── <skill-name>/
│       └── SKILL.md                      # cross-cutting Claude Code skills, auto-discovered by the plugin
│
├── drawio/
│   ├── README.md                          # setup + how this relates to Diagrams_with_Draw.io
│   ├── specs/
│   │   ├── DRAW_IO_STANDARDS.md
│   │   └── styles/expertflow.json        # the `expertflow` style preset
│   ├── templates/                        # generic starter .drawio files
│   ├── skill/                            # drawio-skill (MIT), vendored via git subtree — Claude Code toolset
│   └── source/
│       └── ef-drawio-standards.mdc       # Cursor rule — points at specs/ + style preset
│
├── .github/
│   └── copilot-instructions.md -> ../AGENTS.md    # symlink — GitHub Copilot
│
├── java/
│   ├── VERSION
│   ├── cursorignore.template
│   ├── services.txt
│   └── source/
│       ├── ef-spring-boot-base.mdc       # rule body + frontmatter (description/globs/alwaysApply) in one file
│       ├── ef-graphql-dgs.mdc
│       ├── ef-infrastructure.mdc
│       ├── ef-testing.mdc
│       └── SERVICE-OVERLAY.template.md   # hand-copied per consumer repo, not a synced rule
│
├── angular/
│   ├── VERSION
│   ├── cursorignore.template
│   └── source/
│       ├── ef-angular-base.mdc
│       ├── ef-testing.mdc
│       └── APP-OVERLAY.template.md
│
└── node/
    ├── VERSION
    ├── cursorignore.template
    └── source/
        ├── ef-node-base.mdc
        ├── ef-infrastructure.mdc
        ├── ef-testing.mdc
        └── SERVICE-OVERLAY.template.md
```

Each `.mdc` file already carries the Cursor frontmatter (`description`, `globs`, `alwaysApply`) inline — there is no separate `.meta` file and no `sync.sh` generation step. Cursor reads `.mdc` files directly; every other tool reads `AGENTS.md`.

**Why `CLAUDE.md` is a plain pointer file, not a symlink:** symlinks require Developer Mode or admin rights on Windows, and Git for Windows can check them out as plain text files containing the target path instead of a real link if `core.symlinks` isn't configured. A hard link avoids that but silently goes stale after a clone, since Git doesn't preserve hard-link relationships across checkout — it commits file *content*, not the link itself. A one-line pointer file (`CLAUDE.md` says "read `AGENTS.md`") sidesteps both problems: it's an ordinary text file on every OS, and it's always up to date because it never duplicates content.

---

## How each tool consumes this repo

| Tool | What it gets | How |
|---|---|---|
| Claude Code | Always-on context | `/plugin marketplace add expertflow/ai-standards` |
| Cursor | Per-stack, glob-scoped rules | Symlink `.cursor/rules -> .ai-standards/<stack>/source/` |
| GitHub Copilot | Always-on context | Symlink via `.github/copilot-instructions.md` |
| Codex / Gemini / Cline | Always-on context | Reads `AGENTS.md` directly |

---

## Onboarding a new service repo

```bash
# pull org standards as a subtree
git subtree add --prefix=.ai-standards \
  https://gitlab.expertflow.com/general/ai-standards.git main --squash

# set up symlinks for each tool — pick the stack(s) this repo uses
ln -sfn ../../.ai-standards/java/source .cursor/rules      # Java service example
ln -sfn .ai-standards/AGENTS.md .github/copilot-instructions.md

# Claude Code — register plugin marketplace
/plugin marketplace add expertflow/ai-standards
```

**First-time only** — create a project overlay in the target repo:

```bash
# Java:    java/source/SERVICE-OVERLAY.template.md
# Angular: angular/source/APP-OVERLAY.template.md
# Node:    node/source/SERVICE-OVERLAY.template.md
# Copy, edit, save as .cursor/rules/{project-name}.mdc — never overwritten by future syncs
```

## Syncing org standard updates

```bash
git subtree pull --prefix=.ai-standards \
  https://gitlab.expertflow.com/general/ai-standards.git main --squash
```

---

## Layered precedence

```
~/.codex/AGENTS.md           # global developer preferences
    ↓
.ai-standards/AGENTS.md      # org-wide rules (this repo)
    ↓
repo-root/AGENTS.md          # service-specific rules
    ↓
AGENTS.local.md              # developer personal overrides (gitignored)
    ↓
services/xyz/AGENTS.md       # subdirectory overrides
```

Each level only needs to define what differs from the level above.

---

## Platform team workflow

1. Edit `<stack>/source/*.mdc` directly (frontmatter + body, one file)
2. Bump `<stack>/VERSION`
3. Consumer repos pick up changes via `git subtree pull` (see above)

## Relationship to `EF-AgenticOrg`

`EF-AgenticOrg/guidelines/engineering/ENG-00x` entries are the governance/policy layer (severity, audience, eval criteria) — they should reference the rule content here rather than duplicate it.
