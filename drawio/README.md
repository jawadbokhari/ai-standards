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

## Contents

| Path | What it is |
|---|---|
| `specs/DRAW_IO_STANDARDS.md` | Canvas settings, layer/grouping rules, shape libraries, XML-authoring rules for LLMs (including the Layout & Alignment Checklist), file/folder naming conventions |
| `specs/styles/expertflow.json` | The `expertflow` style preset — palette, shapes, fonts, edge styling. Marked `"default": true` so it applies automatically once linked into place. |
| `templates/` | Generic starter `.drawio` files (entity-relationship, data-flow, sequence-flow, system-architecture, deployment, BPMN process flow, etc.) with no Expertflow-specific content |

## Setup

1. Install the draw.io desktop CLI (needed to render/export `.drawio` files):
   - macOS: `brew install --cask drawio`
   - Windows/Linux: see the [drawio-skill install guide](https://github.com/Agents365-ai/drawio-skill/blob/main/docs/INSTALL_CLI.md)
2. Install this repo as a Claude Code plugin (see the top-level [`README.md`](../README.md)):
   ```
   /plugin marketplace add expertflow/ai-standards
   ```
3. Install the [`drawio-skill`](https://github.com/Agents365-ai/drawio-skill) Claude Code skill — this repo supplies the Expertflow-specific standard and style preset that skill reads, not the skill itself.
4. Link the style preset into place (the skill reads presets from `~/.drawio-skill/styles/`):
   ```bash
   mkdir -p ~/.drawio-skill/styles
   ln -sf "$(pwd)/drawio/specs/styles/expertflow.json" ~/.drawio-skill/styles/expertflow.json
   ```

Once set up, ask Claude Code for a diagram — it will apply the `expertflow` preset and follow
`DRAW_IO_STANDARDS.md`.

## Working with real Expertflow diagrams

For actual product/architecture diagrams, work in the private
[`Diagrams_with_Draw.io`](https://github.com/expertflow/Diagrams_with_Draw.io) repo — it's set up
the same way and follows the same standard, it just also holds internal-sensitive content that
doesn't belong in a public repo.
