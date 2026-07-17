# ai-standards

Central **platform coding standards** for EF projects, consumable by any AI coding agent (Claude Code, Cursor, GitHub Copilot, Cline, Gemini CLI, Codex CLI) via a single `AGENTS.md`-based structure.

This repo replaces [`ef-coding-standards`](https://gitlab.expertflow.com/general/ef-coding-standards)'s Cursor-only `sync.sh` generation approach: each rule file (`<stack>/source/*.mdc`) now carries its Cursor frontmatter (`description`, `globs`, `alwaysApply`) inline, so Cursor reads it directly and every other tool reads `AGENTS.md` — no generation step, no shell scripts.

See [`AGENTS.md`](AGENTS.md) for repository structure, per-tool consumption, and onboarding instructions.

## Maintainer notes

- **`CLAUDE.md` is a plain pointer file, not a symlink** — unlike `.github/copilot-instructions.md`. Don't "fix" this inconsistency by re-symlinking it: symlinks need Developer Mode/admin rights on Windows and Git for Windows can check them out as plain text containing the target path instead of a real link. A hard link avoids that but goes stale silently, since Git doesn't preserve hard-link relationships across clone/checkout. The pointer file works identically on every OS and never goes stale.

## Stacks

| Folder | Stack |
|--------|-------|
| `java/` | Spring Boot 3.5, Java 17, Mongo, Redis (Jedis), Artemis |
| `angular/` | Angular 14, NgModule, Jest |
| `node/` | Node 18+, Express, CommonJS |

## Cross-cutting standards

| Folder | What it covers |
|--------|--------|
| [`drawio/`](drawio/) | Draw.io diagram standard — style preset, generation rules, generic starter templates. See [`drawio/README.md`](drawio/README.md). |
