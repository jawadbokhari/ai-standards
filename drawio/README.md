# drawio

Expertflow's draw.io diagram standard — style preset, generation rules, and generic starter
templates. Cross-cutting (not tied to `java/`, `angular/`, or `node/`) and distributed the same
way as the rest of this repo, via this repo's Claude Code plugin.

This folder holds the **reusable, non-confidential** half of the standard, published here for
org-wide distribution. The private
[`Diagrams_with_Draw.io`](https://github.com/expertflow/Diagrams_with_Draw.io) repo is the source
of truth — it's where the standard is actually edited and where real Expertflow reference diagrams
(product architecture, domain-ownership maps, BPMN process flows) live, which is why that repo
stays private. When `specs/DRAW_IO_STANDARDS.md`, the style preset, or the templates change there,
copy the update here too.

## Which repo do I use?

| You are... | Use... |
|---|---|
| Any engineer who wants Expertflow-branded draw.io diagrams | **This repo**, via `/plugin marketplace add expertflow/ai-standards` below. That's the whole setup. |
| Editing the standard itself, or working with real (confidential) Expertflow architecture diagrams | The private [`Diagrams_with_Draw.io`](https://github.com/expertflow/Diagrams_with_Draw.io) repo instead |
| — | **Never** install the `drawio` skill/plugin directly from Agent365's `365-skills` marketplace. It gives you the raw upstream tool with no Expertflow preset, no `DRAW_IO_STANDARDS.md`, and no version pinning, so your diagrams won't match anyone else's. If you've already done this, uninstall it and switch to the marketplace add below instead. |

## Contents

| Path | What it is |
|---|---|
| `specs/DRAW_IO_STANDARDS.md` | Canvas settings, layer/grouping rules, shape libraries, XML-authoring rules for LLMs (including the Layout & Alignment Checklist), file/folder naming conventions |
| `specs/styles/expertflow.json` | The `expertflow` style preset — palette, shapes, fonts, edge styling. Marked `"default": true` so it applies automatically once linked into place. |
| `templates/` | Generic starter `.drawio` files (entity-relationship, data-flow, sequence-flow, system-architecture, deployment, BPMN process flow, etc.) with no Expertflow-specific content |
| `skill/` | [`drawio-skill`](https://github.com/Agents365-ai/drawio-skill) (MIT), vendored in via `git subtree` so Claude Code users get the full toolset with one clone — no separate install step, no external dependency at use-time |
| `source/ef-drawio-standards.mdc` | Cursor rule (glob: `**/*.drawio`) — points Cursor at `specs/DRAW_IO_STANDARDS.md` and the style preset, same convention as `java/`, `angular/`, `node/` |

## Setup

### Claude Code

1. Install the draw.io desktop CLI (needed to render/export `.drawio` files):
   - macOS: `brew install --cask drawio`
   - Windows/Linux: see [`skill/docs/INSTALL_CLI.md`](skill/docs/INSTALL_CLI.md)
2. Install this repo as a Claude Code plugin (see the top-level [`README.md`](../README.md)):

   ```bash
   /plugin marketplace add expertflow/ai-standards
   ```

   The plugin surfaces `skill/skills/drawio-skill/SKILL.md` — the vendored skill — directly; no
   separate skill install needed.
3. Link the style preset into place (the skill reads presets from `~/.drawio-skill/styles/`):
   ```bash
   mkdir -p ~/.drawio-skill/styles
   ln -sf "$(pwd)/drawio/specs/styles/expertflow.json" ~/.drawio-skill/styles/expertflow.json
   ```

Once set up, ask Claude Code for a diagram — it will apply the `expertflow` preset and follow
`DRAW_IO_STANDARDS.md`.

### Cursor

Symlink this folder's `source/` into `.cursor/rules` the same way as the other stacks (see the
top-level [`README.md`](../README.md) onboarding steps):

```bash
ln -sfn ../../.ai-standards/drawio/source .cursor/rules/drawio
```

Cursor doesn't run the `drawio-skill` toolset (that's Claude Code-specific) — the rule just points
the agent at `DRAW_IO_STANDARDS.md` and the style preset so `.drawio` edits stay consistent with
the standard even without the skill's automation.

## Keeping the vendored skill current

`skill/` is a `git subtree`, not a copy-paste. To pull upstream updates:

```bash
git subtree pull --prefix=drawio/skill https://github.com/Agents365-ai/drawio-skill.git main --squash
```

Do this periodically (upstream is under active development) rather than letting it drift silently.
Last pulled: 2026-09-11, upstream commit `cfe6131` (added `diagramctl.py`, an MCP server, and the
diagram-IR sync/test/policy tooling — a large jump from the previously vendored 1.34.0).

## Working with real Expertflow diagrams

For actual product/architecture diagrams, work in the private
[`Diagrams_with_Draw.io`](https://github.com/expertflow/Diagrams_with_Draw.io) repo — it's set up
the same way and follows the same standard, it just also holds internal-sensitive content that
doesn't belong in a public repo.
