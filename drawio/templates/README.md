# Draw.io Templates

Starter templates for Expertflow diagrams. Copy a template to the appropriate folder before editing — never edit these files directly.

Before using any of these, confirm draw.io is actually the right tool — see [DIAGRAM_TOOL_SELECTION.md](../../specs/DIAGRAM_TOOL_SELECTION.md) for when to use Mermaid instead. For full standards, color palette, and LLM instructions see [DRAW_IO_STANDARDS.md](../../specs/DRAW_IO_STANDARDS.md).

| Template | File | Use Case | Shape Libraries |
|---|---|---|---|
| System Architecture | [system-architecture.drawio](system-architecture.drawio) | Component maps, service layers, platform overview | Built-in: rounded rectangle, swimlane |
| Sequence Flow | [sequence-flow.drawio](sequence-flow.drawio) | API call chains, conversation routing, message flows | Built-in: rectangle, dashed line, arrow |
| Network / Infrastructure | [network-infrastructure.drawio](network-infrastructure.drawio) | Kubernetes clusters, Docker topology, network layout | `mxgraph.network.*`, built-in containers |
| Process / Workflow | [process-workflow.drawio](process-workflow.drawio) | BPMN processes, approval flows, business procedures | `mxgraph.flowchart.*` |
| Data Flow | [data-flow.drawio](data-flow.drawio) | ETL pipelines, data movement, DFDs | `mxgraph.flowchart.*` |
| Entity Relationship | [entity-relationship.drawio](entity-relationship.drawio) | DB schemas, data models, domain models | Built-in: table/entity shapes |
| User Journey | [user-journey.drawio](user-journey.drawio) | CX journey maps, customer experience stages | Built-in: rectangle, text |
| Deployment | [deployment.drawio](deployment.drawio) | Service deployment, port maps, infrastructure topology | Built-in: rectangle, dashed containers |
| Layer/Band Architecture (reference example) | [platform-architecture-layer-band.drawio](platform-architecture-layer-band.drawio) | Wide, horizontal-layer platform architecture with product-branded palette and icons — see note below | Built-in: swimlane, inline SVG icons |

## How to use

1. Identify the right template using the decision tree in `DRAW_IO_STANDARDS.md` §8.5
2. Copy the template file to the appropriate subfolder (`diagrams/architecture/`, `diagrams/flows/`, etc.)
3. Rename it following the naming convention: `lowercase-kebab-case.drawio`
4. Replace all `[Placeholder]` labels with real content
5. Apply the semantic color palette (§3) to any new elements you add

**Note on `platform-architecture-layer-band.drawio`:** unlike the other templates, this one is not a blank placeholder starter — it's the real ExpertFlow CX Headless Platform Architecture diagram, kept as a worked reference example. Use it to see the layer/band pattern applied end-to-end: design-system-token palette (§3.0), optional header icons (§7.1), wide-diagram typography (§4), and the tightened sibling-gap spacing (§5) all in one file. Copy the structural patterns, not the specific labels/content.
