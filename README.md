# ai-standards

Central **platform coding standards** for EF projects, consumable by any AI coding agent (Claude Code, Cursor, GitHub Copilot, Cline, Gemini CLI, Codex CLI) via a single `AGENTS.md`-based structure.

This repo replaces [`ef-coding-standards`](https://gitlab.expertflow.com/general/ef-coding-standards)'s Cursor-only `sync.sh` generation approach: each rule file (`<stack>/source/*.mdc`) now carries its Cursor frontmatter (`description`, `globs`, `alwaysApply`) inline, so Cursor reads it directly and every other tool reads `AGENTS.md` — no generation step, no shell scripts.

See [`AGENTS.md`](AGENTS.md) for repository structure, per-tool consumption, and onboarding instructions.

## Stacks

| Folder | Stack |
|--------|-------|
| `java/` | Spring Boot 3.5, Java 17, Mongo, Redis (Jedis), Artemis |
| `angular/` | Angular 14, NgModule, Jest |
| `node/` | Node 18+, Express, CommonJS |
