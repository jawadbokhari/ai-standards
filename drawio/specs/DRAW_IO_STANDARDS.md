# Expertflow Draw.io Standards & LLM Guidelines

> **Version:** 2.0 | **Last updated:** 2026-07-15 | **Owner:** Product Team

This document is the source of truth for the parts of Expertflow draw.io diagram-making that **drawio-skill-external does not cover on its own**: folder/naming conventions, canvas settings, layer/grouping and spacing rules, and the layout/alignment checklist. It is written for both human team members and AI agents (LLMs).

> **Palette, shapes, fonts, and edge styling are no longer defined here.** They're now owned by the `expertflow` style preset at `~/.drawio-skill/styles/expertflow.json`, used by the [drawio-skill-external](https://github.com/Agents365-ai/drawio-skill) skill (set as that skill's default preset — it applies automatically, no need to name it). See that file for the current palette/shape/edge/font values. If you need to change the palette, edit the preset, not this document.
>
> **Superseded / retired as of v2.0** (previously lived in this document, now either replaced by the preset or dropped outright by decision): the Semantic Color Palette section, the design-tokens-to-diagram-role mapping, the two-tier headline+description font trick, Connection Styles, Material Symbols header icons, and the `.drawio.svg` GitHub-viewable export convention (diagrams now export using drawio-skill-external's own default formats/flags instead). `design-tokens-default.md` in this folder is kept only as the historical source the `expertflow` preset was derived from.
>
> **Tool selection (draw.io vs. Mermaid):** `DIAGRAM_TOOL_SELECTION.md` is currently unmaintained pending a separate, dedicated Mermaid skill — do not treat it as an active gate right now.

---

## 1. File & Folder Naming

### Rules

- **File names:** lowercase kebab-case, descriptive, `.drawio` extension
  - ✅ `cx-routing-engine-flow.drawio`
  - ❌ `CX Flow v2 FINAL.drawio`
- **Folder structure:**
  ```
  diagrams/
  ├── templates/          ← starter templates (do not edit directly)
  ├── architecture/       ← system and component architecture
  ├── flows/              ← sequence and process flows
  ├── infrastructure/     ← network, deployment, Kubernetes
  ├── data/               ← ER diagrams, data flow diagrams
  ├── ux/                 ← user journeys, CX maps
  └── [project-name]/     ← project-specific one-off diagrams
  ```
  This structure applies inside the `Diagrams_with_Draw.io` repo. **Diagrams authored inside a different project's own repo** (e.g. `platform-architecture/`) are not required to nest under a `diagrams/` root — keep them wherever that project's repo puts them — but the naming, canvas, and layout rules elsewhere in this document still apply.
- **Versioning:** Append `-v2`, `-v3` to file names when keeping history. Delete superseded versions after team review.
- **Exported images:** use whatever format/flags drawio-skill-external's own export step produces (see that skill's `SKILL.md` — currently PNG with `-e -s2` by default, or SVG/PDF on request). No Expertflow-specific export convention is enforced here anymore.

---

## 2. Canvas Settings

Apply these settings to every new diagram (`Edit → Diagram` in draw.io):

| Setting | Value |
|---------|-------|
| Page size | A4 Portrait (827 × 1169 px) — reference only, not a hard constraint |
| Grid | Enabled, 10px |
| Guides | Enabled |
| Page view | Enabled |
| Shadow | Disabled |
| Math | Disabled |

> **Exception:** `user-journey.drawio` uses A4 Landscape (1169 × 827 px) because a 5-stage journey grid cannot fit in portrait width. When creating journey maps, use `pageWidth="1169" pageHeight="827"`. **Font default:** Helvetica is set per-cell via `fontFamily=Helvetica` in each cell's `style` string (or via the active preset's `font.fontFamily`). There is no global font attribute on `mxGraphModel`.

**XML attributes to set on `mxGraphModel`:**

```xml
<mxGraphModel grid="1" gridSize="10" guides="1"
              pageWidth="827" pageHeight="1169" pageScale="1"
              math="0" shadow="0" page="1">
```

**Diagrams are NOT required to fit within a page boundary.** Content should use as much canvas as the concept requires for human readability. Never compress, scale down, or split a diagram just to fit it within a page — doing so reduces readability and hides the structure of the process. Let the canvas extend as far as needed.

---

## 3. Layers & Grouping

- **Use swimlane containers** to group related elements. Each architectural layer or zone should be a named swimlane.
- **Container style:** colors come from the active preset (`~/.drawio-skill/styles/expertflow.json`), not hardcoded here. Use `fontStyle=1` (bold) for header text.
- **Nesting depth:** Maximum 2 levels (container → element) for most diagram types. **Exception:** infrastructure and deployment diagrams may use 3 levels (e.g. cluster → namespace → service, or zone → host → container) because Kubernetes topology inherently requires it. Never exceed 3 levels.
- **Lane label strip:** Use `startSize=30` on all swimlane lanes. This keeps the label strip narrow so it does not consume canvas space that shapes and connectors need.
- **Padding:** Leave at least 20px between the container border and any child element. **Exception for swimlane label strips:** with `horizontal=0` (label on left), the label strip is `startSize` px wide — child shapes must have `x >= startSize + 10` (not just 20px from the outer border) or they will overlap the label text. With `horizontal=1` (default, label on top), the equivalent rule applies to `y`.
- **Minimum horizontal gap between adjacent shapes:** 40px (right edge of one shape to left edge of the next). This gives connectors a clear travel lane and keeps the diagram readable. For small symbols (start/end events, ≤ 40px wide) a 30px gap is acceptable.
  - **This is a floor for connector clarity, not a fixed value to apply everywhere.** It compounds fast: a lane with `N` sibling shapes has `N-1` gaps, so at `N=7` a uniform 40px gap alone adds 240px of pure whitespace to that lane's width — often the single biggest contributor to a diagram's total canvas size. When a lane holds more than ~4 sibling shapes with no connectors routed *between* them (i.e. they're just laid out side by side, not individually wired to each other), a tighter 20–24px gap is acceptable and keeps the container from growing unnecessarily wide. Reserve the full 40px for gaps that a connector actually routes through.
- **Vertical centering in lanes:** Shapes inside a lane should be vertically centered: `shape_y = (lane_height − shape_height) / 2`. For `horizontal=0` lanes this is pure vertical centering; the label strip is on the left and does not affect the vertical axis.
- **Alignment:** Snap all nodes to the 10px grid.

**Swimlane container XML pattern** (colors illustrative only — use the active preset's resolved fill/stroke for the role in question):

```xml
<mxCell id="layer-client" value="Client / UI"
  style="swimlane;startSize=30;fillColor=#E3F2FD;strokeColor=#1565C0;
  fontColor=#1565C0;fontStyle=1;fontSize=11;fontFamily=Helvetica;"
  vertex="1" parent="1">
  <mxGeometry x="50" y="80" width="727" height="120" as="geometry"/>
</mxCell>

<!-- Child node — geometry is relative to the container's top-left corner -->
<mxCell id="agent-desk" value="Agent Desk"
  style="rounded=1;whiteSpace=wrap;html=1;fillColor=#E3F2FD;strokeColor=#1565C0;
  fontFamily=Helvetica;fontSize=10;"
  vertex="1" parent="layer-client">
  <mxGeometry x="60" y="45" width="160" height="50" as="geometry"/>
</mxCell>
```

---

## 4. Shape Libraries by Diagram Type

| Diagram Type | Recommended Shapes / Libraries |
|---|---|
| System Architecture | Built-in: rounded rectangle, swimlane container |
| Sequence Flow | Built-in: rectangle (participant), dashed line (lifeline), arrow |
| Network / Infrastructure | `mxgraph.network.*` or built-in shapes for Kubernetes; dashed containers for boundaries |
| Process / Workflow | `mxgraph.flowchart.*`: start (circle), task (rectangle), gateway (diamond), end (thick circle) |
| Data Flow | `mxgraph.flowchart.*`: external entity (rectangle), process (circle/ellipse), data store (open rect) |
| Entity Relationship | Swimlane container per entity (header + attribute rows as children). Each entity is one `swimlane` cell; attributes are child `text` cells. This keeps entities moveable as a unit. |
| User Journey | Built-in: rectangles, text cells, colored row backgrounds |
| Deployment | Built-in + `mxgraph.network.*`: dashed container for zones, service boxes with port labels |

**Enabling libraries in draw.io:** `View → Shapes → check the required library`. For offline/embedded use, rely only on `mxgraph.flowchart.*` built-ins. The `mxgraph.network.*` shapes (e.g. `mxgraph.network.load_balancer`, `mxgraph.network.workstation`) require the optional Network library to be enabled — if unavailable they render as blank rectangles. Substitute with plain `rounded=1` rectangles and text labels when offline rendering is required.

---

## 5. LLM Instructions

This section is written for AI agents. Follow these instructions exactly when generating or modifying `.drawio` files.

### 5.1 Understanding the `.drawio` XML Structure

Every `.drawio` file is XML with this exact hierarchy:

```
mxfile
└── diagram  (one per page/tab)
    └── mxGraphModel  (canvas settings as XML attributes)
        └── root
            ├── mxCell id="0"              ← root layer (NEVER modify or remove)
            ├── mxCell id="1" parent="0"   ← default page parent (NEVER modify or remove)
            └── mxCell id="..." ...        ← all content cells
```

**Rules for all content cells:**

- `id` — unique string, no spaces, no duplicates within the file
- `parent` — either `"1"` (page root) or a container cell's `id`
- `vertex="1"` for nodes/shapes, `edge="1"` for connections (never both)
- `value` — the display label (use `""` for invisible/unlabeled cells)
- `mxGeometry` child element — always required

**XML character escaping in `value` attributes** — these characters MUST be escaped or the file will be invalid:

| Character | Use in `value` | Example |
| --- | --- | --- |
| `&` (ampersand) | `&amp;` | `Auth &amp; Rate Limiting` |
| `<` (less-than) | `&lt;` | `Request &lt; 1MB` |
| `>` (greater-than) | `&gt;` | `Response &gt; 200ms` |
| `"` (double-quote) | `&quot;` | Only needed inside attribute values |
| Line break | `&#xa;` | `Service A&#xa;Port: 8080` |

### 5.2 Adding a New Node

```xml
<!-- Step 1: choose a unique id (descriptive kebab-case) -->
<!-- Step 2: choose style from the active preset (~/.drawio-skill/styles/expertflow.json) based on the node's role -->
<!-- Step 3: set parent="1" or parent="<container-id>" if inside a layer -->
<!-- Note: if inside a container, x/y are relative to that container's top-left -->

<mxCell id="cim-backend" value="CIM Backend"
  style="rounded=1;whiteSpace=wrap;html=1;
  fillColor=#ECF3FF;strokeColor=#1a50a3;
  fontFamily=Helvetica;fontSize=12;"
  vertex="1" parent="layer-core">
  <mxGeometry x="40" y="50" width="150" height="60" as="geometry"/>
</mxCell>
```

### 5.3 Adding a New Edge (Connection)

```xml
<!-- source and target must be id values of existing vertex cells -->
<!-- parent is "1" (page root) for most diagrams -->

<mxCell id="edge-gw-cim" value="REST"
  style="edgeStyle=orthogonalEdgeStyle;rounded=1;orthogonalLoop=1;jettySize=auto;html=1;strokeWidth=1.5;
  fontFamily=Helvetica;fontSize=9;"
  edge="1" source="api-gateway" target="cim-backend" parent="1">
  <mxGeometry relative="1" as="geometry"/>
</mxCell>
```

> **Swimlane pool exception:** In process-workflow diagrams that use a swimlane pool container, edges connecting nodes in *different* lanes must use `parent="<pool-id>"`. Edges connecting nodes in the *same* lane use `parent="<lane-id>"`. This is required because draw.io resolves edge coordinates relative to the nearest common ancestor container. The templates demonstrate the correct parent assignment for each case.

### 5.4 Updating an Existing Node's Label

Locate the `mxCell` by its `id` (preferred) or by its current `value` if the `id` is unknown. If two cells share the same `value` (e.g. two `[Service A]` placeholders), you **must** use `id` to distinguish them. Change **only** the `value` attribute. Leave `id`, `style`, `parent`, `vertex`/`edge`, and `mxGeometry` completely unchanged.

```xml
<!-- Before -->
<mxCell id="svc-a" value="[Service A]" style="rounded=1;..." vertex="1" parent="layer-core">
  <mxGeometry x="40" y="50" width="140" height="60" as="geometry"/>
</mxCell>

<!-- After — only value changed -->
<mxCell id="svc-a" value="Conversation Manager" style="rounded=1;..." vertex="1" parent="layer-core">
  <mxGeometry x="40" y="50" width="140" height="60" as="geometry"/>
</mxCell>
```

### 5.5 Choosing the Right Template

```
START: What does this diagram primarily show?
│
├── How things are STRUCTURED / ORGANIZED
│   ├── Logical: services, components, API relationships → system-architecture.drawio
│   ├── Physical: running containers, hosts, ports, protocol labels
│   │   ├── Need to show Kubernetes namespaces / pod topology? → network-infrastructure.drawio
│   │   └── Need to show host/VM groupings, port numbers, network zones? → deployment.drawio
│   └── Tiebreaker (network vs. deployment): if the primary focus is
│       K8s-native objects (namespace, pod, service, ingress) → network-infrastructure.drawio
│       If the primary focus is hosts, VMs, or Docker Compose stacks → deployment.drawio
│
├── How things HAPPEN OVER TIME / in SEQUENCE
│   ├── Does it involve human actors performing tasks with decisions?
│   │   └── YES → process-workflow.drawio
│   ├── Messages / API calls between services (no human decision flow)?
│   │   └── YES → sequence-flow.drawio
│   └── How DATA is transformed as it moves between systems (ETL, pipelines)?
│       └── YES → data-flow.drawio
│   Tiebreaker (sequence vs. process): if the diagram has swimlane lanes
│   for human roles / teams → process-workflow. If it's purely
│   service-to-service API calls → sequence-flow.
│
├── Data SCHEMA / model (entities, attributes, relationships) → entity-relationship.drawio
│
├── Customer EXPERIENCE / journey stages → user-journey.drawio
│
└── None of the above → use system-architecture.drawio as a general-purpose fallback
    and add a comment noting the diagram type is not yet templated.
```

### 5.6 Generating a Complete New Diagram

1. Use the decision tree in 5.5 to pick the right template from `diagrams/templates/`
2. Copy the template to the appropriate folder (see Section 1 folder structure)
3. Update the title cell — find the `mxCell` whose `id` ends in `-title` (e.g. `sa-title`, `sq-title`). Its `value` contains a bracketed placeholder like `[Diagram Title]`, `[Sequence Flow Title]`, etc.
4. Replace all placeholder labels — anything in `[Square Brackets]` — with real content
5. Apply colors/shapes/fonts/edges from the active preset (`~/.drawio-skill/styles/expertflow.json` — applied automatically by drawio-skill-external)
6. Connect elements using edges; dashing follows the preset's `edges.dashedFor` list (async/event/webhook/optional/dependency wording)
7. **Validate before outputting:** every `source` and `target` in edge cells must match an existing node `id`
8. Output the **complete, valid XML** — never truncate or omit cells

### 5.7 Modifying an Existing Diagram

1. Parse the full XML before making any changes
2. Identify cells to modify by `id` or `value`
3. Make only the targeted changes — do not reformat, reorder, or reindent unrelated cells
4. **Never rename an `id`** — doing so breaks all edges that reference it
5. After changes, verify all edge `source`/`target` values still resolve to existing cell `id`s
6. Output the complete modified XML

### 5.8 Layout & Alignment Checklist

Run these checks **before outputting any diagram XML**, whether generating from scratch or modifying an existing file.

#### A. Swimlane child placement

For every child shape inside a swimlane container:

- **`horizontal=0` (label on left):** child `x` must be `>= startSize + 10`. If `startSize=90`, the minimum child `x` is `100`. Anything less overlaps the label strip.
- **`horizontal=1` (label on top, default):** child `y` must be `>= startSize + 10`.
- Verify the child's right/bottom edge does not exceed the container's `width`/`height` minus 20px.

```xml
<!-- ✅ Correct: startSize=90, horizontal=0, child x=100 -->
<mxCell id="pw-start" ... parent="pw-lane1">
  <mxGeometry x="100" y="145" width="60" height="60" as="geometry"/>
</mxCell>

<!-- ❌ Wrong: x=40 is inside the 90px label strip -->
<mxCell id="pw-start" ... parent="pw-lane1">
  <mxGeometry x="40" y="150" width="60" height="60" as="geometry"/>
</mxCell>
```

#### B. Explicit connection anchors on every edge

Every edge **must** declare `exitX`, `exitY`, `exitDx`, `exitDy`, `entryX`, `entryY`, `entryDx`, `entryDy` in its style string. Without these, Draw.io picks connection points dynamically and can route arrows through shapes or produce unexpected bends as the diagram is moved or resized.

For straight horizontal (left-to-right) flows, verify that the **vertical centers of source and target are equal**:

```
center_y = shape_y + (shape_height / 2)
```

If centers differ, orthogonal routing adds an unwanted bend. Fix by aligning `y` values so both centers match.

**Node → tall-container edges.** When one end is a small node and the other is a large container (e.g. a swimlane panel), remember that `exitY=0.5`/`entryY=0.5` anchors to **each shape's own center** — and a tall container's center can sit far from the node, even when the node visually points at the top of the panel. The fractional anchor `0.5` resolves to `container_y + (container_height / 2)`, not to the node's level. To keep the edge straight:

- Align the node's page-center to the container's page-center so both `entryY=0.5`/`exitY=0.5` resolve to the same `y` (preferred — keeps clean `0.5` anchors), **or**
- Set the container-side fractional anchor to the node's center: `entryY = (node_center_y_page − container_y) / container_height`.

A few-pixel mismatch here is the most common cause of a "nearly straight but kinked" connector. When aligning the node's center, adjust its `height` (and lane bounds per check A) rather than nudging `y` off the 10px grid.

Correct anchor style for left-to-right flow:

```xml
style="...exitX=1;exitY=0.5;exitDx=0;exitDy=0;entryX=0;entryY=0.5;entryDx=0;entryDy=0;"
```

#### C. Cross-lane edges

Edges between shapes in different swimlane containers (`parent="1"`) must have **explicit waypoints** or draw.io will auto-route them through intervening shapes. Route via the gap between lanes:

1. Calculate the gateway/source bottom in page coordinates: `lane_y + shape_y + shape_height`
2. Place waypoint 1 just above the lane boundary: `(source_center_x_page, lane1_y + lane1_height - 10)`
3. Place waypoint 2 directly above the target: `(target_center_x_page, lane1_y + lane1_height - 10)`
4. Use `exitY=1` (bottom exit) and `entryY=0` (top entry) to anchor both ends

```xml
<mxCell id="pw-edge-gw-t3" value="Yes"
  style="edgeStyle=orthogonalEdgeStyle;html=1;strokeWidth=1.5;
  fontFamily=Helvetica;fontSize=9;
  exitX=0.5;exitY=1;exitDx=0;exitDy=0;
  entryX=0.5;entryY=0;entryDx=0;entryDy=0;"
  edge="1" source="pw-gw1" target="pw-task-3" parent="1">
  <mxGeometry relative="1" as="geometry">
    <Array as="points">
      <mxPoint x="695" y="430"/>  <!-- just above lane boundary -->
      <mxPoint x="290" y="430"/>  <!-- directly above target -->
    </Array>
  </mxGeometry>
</mxCell>
```

#### D. Minimum shape spacing

Before placing shapes, verify that every adjacent pair of shapes **that has a connector routed between them** has at least **40px of clear horizontal gap** (right edge to left edge). For small symbols (start/end events ≤ 40px wide) 30px is the minimum. This gap is the connector's travel lane — anything narrower makes arrows hard to trace.

```text
gap = next_shape_x − (prev_shape_x + prev_shape_width)   ← must be ≥ 40 (or ≥ 20 for unconnected siblings, see Section 3)
```

For shapes that are laid out side-by-side with **no connector between them** (e.g. a row of parallel sibling nodes inside one lane, each independently wired to something outside the lane) — see Section 3's note on gap scaling: 40px applied uniformly across many siblings inflates canvas width fast, so 20–24px is acceptable there.

If a lane or pool is not wide enough to maintain the required gaps at the required number of shapes, **expand the pool/lane width**. Do not compress shapes below the applicable minimum, or reduce gaps to fit a page boundary.

#### E. No-page-constraint check

After laying out all shapes, do **not** resize, scale, or reflow them to bring content within the page boundary. The page boundary in Draw.io is a visual reference only. Diagrams that extend beyond it are valid and preferred over compressed layouts that sacrifice readability.

#### F. Feedback / loop edges

Edges that loop back to an earlier shape must use explicit waypoints that **clear all shapes in the path**. The safe routing pattern is over the top (or below the bottom) of the lane:

1. Find the topmost `y` of all shapes in the lane
2. Set loop waypoints at `y = topmost_shape_y - 40` (above all shapes)
3. Use `exitY=0` / `entryY=0` to exit and enter from the top, or `exitY=1` / `entryY=1` for a bottom loop
4. The two waypoint `x` values should be the center-x of the exit shape and the center-x of the entry shape

```xml
<mxCell id="pw-edge-gw-no" value="No"
  style="edgeStyle=orthogonalEdgeStyle;html=1;strokeWidth=1.5;dashed=1;
  fontFamily=Helvetica;fontSize=9;
  exitX=0.5;exitY=0;exitDx=0;exitDy=0;
  entryX=0.5;entryY=0;entryDx=0;entryDy=0;"
  edge="1" source="pw-gw1" target="pw-task-1" parent="pw-lane1">
  <mxGeometry relative="1" as="geometry">
    <Array as="points">
      <mxPoint x="645" y="100"/>  <!-- above all shapes (min shape y=135) -->
      <mxPoint x="290" y="100"/>
    </Array>
  </mxGeometry>
</mxCell>
```

---

## 6. Template Quick Reference

See [diagrams/templates/README.md](diagrams/templates/README.md) for the full index with use cases and shape libraries.

| Template | File | Primary Use |
|---|---|---|
| System Architecture | `diagrams/templates/system-architecture.drawio` | Component maps, service layers, platform overview |
| Sequence Flow | `diagrams/templates/sequence-flow.drawio` | API call chains, conversation routing, message flows |
| Network / Infra | `diagrams/templates/network-infrastructure.drawio` | Kubernetes clusters, Docker, network topology |
| Process / Workflow | `diagrams/templates/process-workflow.drawio` | Simple 2-lane approval flows, team handoffs, basic business procedures |
| BPMN Process Flow | `diagrams/templates/bpmn-process-flow.drawio` | Multi-pool BPMN: parallel gateways, subprocesses, timer events, cross-pool message flows |
| Data Flow | `diagrams/templates/data-flow.drawio` | ETL pipelines, data movement, DFDs |
| Entity Relationship | `diagrams/templates/entity-relationship.drawio` | DB schemas, data models, domain models |
| User Journey | `diagrams/templates/user-journey.drawio` | CX journey maps, customer experience stages |
| Deployment | `diagrams/templates/deployment.drawio` | Service deployment, port maps, infrastructure topology |
| Layer/Band Architecture | `diagrams/templates/platform-architecture-layer-band.drawio` | Wide, horizontal-layer platform diagrams. Predates the `expertflow` preset (still uses hardcoded palette/icons) — being re-evaluated as templates are regenerated with drawio-skill-external; see `diagrams/templates/platform-architecture-layer-band.skill-demo.drawio` for a pure-skill-preset comparison build |
