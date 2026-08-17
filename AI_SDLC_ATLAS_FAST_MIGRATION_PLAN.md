# AI-SDLC Atlas Fast Migration Plan

## Outcome

Migrate the proven interaction and diagram mechanics from `ai-sdlc-diagram-migration-evaluation.html` into the production document `ai-sdlc-plugin-research-atlas-v2.html` without losing content, breaking the standalone-HTML model, or deleting the current diagrams before replacements pass.

Speed comes from one shared runtime, a semantic registry, three representative pilots, small reversible commits, and a fixed validation loop. The evaluation file remains reference evidence; it is not copied wholesale.

## Non-negotiable boundaries

- Production target: `ai-sdlc-plugin-research-atlas-v2.html`.
- Preserve `flowDiagram(c)` and `systemDiagram(kind)` as rollback fallbacks until all 20 semantic diagrams pass.
- No frontend framework, bundler, or custom geometry engine.
- Maximum two diagram renderers. Start with Mermaid only; add Graphviz/Viz.js only when a documented hard case fails Mermaid acceptance.
- Gateway owns model routing and usage facts. PMS owns canonical work state. The atlas/plugin owns composition, preflight, evidence, continuity, guidance, and developer-facing controls.
- Do not delete, rewrite, or commit unrelated workspace files.

## Definition inventory and migration order

The atlas has 20 semantic definitions, not merely 20 rendered instances. Shared diagrams repeat across many capability/perspective views, so a single registry definition must be referenced everywhere.

| ID | Current source and category | Required correction | Batch |
|---|---|---|---|
| F00 | Program flow; execution | Split overview from failure detail | Explanatory cleanup |
| F01 | Prompt flow; transformation | Content-aware wrapping | Fast parity |
| F02 | Packet capability flow; processing | Deduplicate against S03 | After S03 |
| F03 | Context flow; retrieval | Authored wrapping | Fast parity |
| F04 | Preflight; decision/process | Separate facts from estimates | Data redesign |
| F05 | Guidance; content workflow | Improve labels and failure treatment | Fast parity |
| F06 | Drift; feedback/state | Explicit state loop | Workflow/state |
| F07 | Goal; hierarchy/workflow | Overview/detail split | Workflow/state |
| F08 | Ledger; lifecycle/state | Preserve persistence states | Fast parity |
| F09 | Focus; explanatory | Reduce to four–six concepts | Explanatory cleanup |
| F10 | Evaluation; evidence feedback | Explicit calibration cycle | Pilot 2 |
| F11 | Research; decision tree | Branch and evidence-gate semantics | Workflow/state |
| F12 | Market; ecosystem/dependency | Stabilise many-to-many model first | Deferred |
| S01 | Company context; C4-style | Split overview/integration detail; real boundaries | Architecture |
| S02 | Product surface; subsystem | Decide restore versus remove; currently unreachable | Reachability decision |
| S03 | Task Memory Packet; nested architecture | Compact boundaries; possible overview/detail | Pilot 1 |
| S04 | Developer execution; sequence | Use a real sequence or two workflows | Sequence validation |
| S05 | Agile team; responsibility/swimlane | True actor lanes and artifact row | Pilot 3 |
| S06 | Roadmap; timeline/dependency | Add visible gates | Fast parity |
| S07 | Token loop; data/feedback | Visually separate facts and estimates | Data redesign |

Runtime occurrence rules to preserve:

- F00–F12 are created by `flowDiagram(c)` in Executive; all except packet also appear in Architecture.
- F02 Architecture uses S03 instead of the generic flow.
- S01 repeats in Executive at applicable verbosity.
- S04, S05, and S07 repeat across Developer Journey, Team & Agile, and Token & Economics.
- S06 repeats in Roadmap.
- S02 exists in source but has no current call site. Decide explicitly; do not silently expose or delete it.

## Target runtime architecture

### Registry

Add one renderer-neutral `diagramRegistry` entry per ID:

```text
id
title
description
category
renderer
source
legacyFallback
heightPolicy
textAlternative
status
```

Views reference registry IDs. They never duplicate diagram source, toolbar markup, IDs, or export handlers.

### Mount component

`diagramMount(id, fallbackHtml)` emits:

1. A theme-matched diagram card.
2. Title and non-colour render status.
3. One shared icon toolbar: zoom out, zoom in, fit, reset, full/window, download.
4. Download dropdown with SVG and PNG.
5. Local viewport with accessible name.
6. Adjacent text alternative.
7. Source box visible only at the atlas's highest verbosity (`5. Raw collection`).
8. Legacy fallback retained but hidden after successful render.

### Lifecycle

- Before `render()` replaces `#main.innerHTML`, call `destroyMountedDiagrams()`.
- Destroy every pan/zoom instance and disconnect its ResizeObserver.
- After inserting the selected capability/perspective, call `observeDiagramMounts(main)`.
- Use one IntersectionObserver rooted at the locally scrolling `main` area.
- Render each mount once on first intersection with a small forward margin.
- Render immediately if IntersectionObserver is unavailable.
- Never use the spike's single `ensureSpikeRendered` flag in production.
- Every rendered instance gets a unique runtime ID even when the semantic registry ID repeats.
- Failure shows a local error, text alternative, and legacy diagram—never a blank viewport.

### Dependencies and CSP

- Initial fast path: exact Mermaid `11.15.0` and svg-pan-zoom `3.6.1` from jsDelivr.
- Update CSP only in the runtime commit: allow exact `https://cdn.jsdelivr.net` script loading.
- Record that this is online standalone HTML, not offline standalone HTML.
- Self-host reviewed pinned assets, checksum records, SBOM, and release review are later hardening gates.
- Configure Mermaid with strict security, deterministic IDs, native SVG labels (`htmlLabels:false`), and theme variables derived from the atlas's active `data-theme`.

## Execution commits

Every numbered item is independently testable, committed, and pushed through SSH immediately after passing.

### 0. Baseline preservation

- Record the current commit hash and screenshots at desktop, constrained desktop, mobile, dark, light, and print.
- Record content purpose and text alternative for F00–F12 and S01–S07.
- Mark redundant, unreachable, split-required, and deferred definitions.
- No production behavior changes.

### 1. Stabilise the actual atlas shell

- Shell equals available viewport: `100dvh` with `100svh` fallback.
- Propagate `min-height:0` through every grid/flex ancestor.
- Disable body/page scrolling; header, title, and horizontal perspectives remain visible.
- Sidebar and content workspace share the same top and bottom baselines.
- Sidebar and active content card become independent local scroll regions only when their content exceeds available height.
- Use overflow measurement to add scrolling only when `scrollHeight > clientHeight`.
- Apply `overscroll-behavior:contain`; prevent scroll chaining.
- Capability or perspective changes never use native anchor scrolling. Update the hash with `history.replaceState`, reset only the active content pane to top, and keep page position fixed.
- On narrow screens, deliberately reflow while retaining local scroll contexts and visible navigation.

### 2. Consolidate header and navigation controllers

- Keep concise title.
- Title info owns the long aim and decision context.
- Metadata icon owns date and tags; exact date label: **Updated on**.
- Shared popover controller supports hover/focus preview, click pin, active colour, second-click close while hovered, outside-click close, Escape, accurate `aria-expanded`, resize positioning, below-first/above-fallback placement, and viewport clamping. Never place popovers to the side.
- Popovers use two nested surfaces, border, and shadow so they never visually fuse with the sidebar or page.
- Retain icon-led verbosity selector, icon-only theme toggle, and print icon.
- Verbosity options use `0.`, `1.`, etc., never an em dash.
- Add Summary at horizontal index 0. Summary owns the problem/intervention/glance block; other perspectives do not carry that persistent block above their content.
- Exactly one perspective panel is visible inside the local content card.
- Tablist supports Left, Right, Home, and End.
- Perspective selection changes only the view of the current capability; it never changes sidebar capability selection.

### 3. Finalise sidebar tree controller

- Maintain real directory hierarchy with visible tree lines and distinct group/leaf styling.
- Groups are one-open-at-a-time accordions.
- Selecting a leaf sets one current item without anchor scrolling.
- Add collapse control at sidebar top right. Collapsed state shows icons only, expands the content area, and refits visible diagrams.
- Icon rule: two-plus words use initials of first two words; one word uses first two letters.
- Permit custom `data-icon` values.
- If derived initials collide, mark them unavailable, show the rare initials-plus-serial 2×2 fallback, and emit a diagnostic requiring a custom icon.
- Popovers open only from subtle info icons, not whole-row hover.
- Group/leaf info uses the shared temporary/pinned popover controller.
- Logical focus order, visible focus, accessible names, and no focus trap are mandatory.

### 4. Add registry and mount without changing call sites

- Add all 20 registry records and `diagramMount` factory.
- Keep legacy generators and current diagram CSS unchanged.
- Add shared card, toolbar, viewport, status, source, and fallback styles.
- Do not replace a production diagram yet.

### 5. Port diagram lifecycle and controls

- Port Mermaid initialization, instances map, padded fit, system-container title padding, render function, ResizeObserver, and delegated toolbar actions.
- Fit/reset is deterministic, centered, and includes visible four-sided padding.
- Reset equals export camera intent.
- Pan and wheel zoom remain inside the local viewport and never move the page.
- Preserve user viewport through unrelated interactions; refit only for explicit fit/reset, first render, or required layout recalculation.
- Full/window is a wrapper state, not a browser Fullscreen dependency.
- Icons are optically centered, theme-aware, keyboard accessible, and labelled.
- Source language is visible only at verbosity 5.

### 6. Harden exact-diagram export before pilots

Port and centralise:

- `safeName`
- computed style inlining and CSS-variable resolution
- portable colour conversion
- text wrapping
- `foreignObject` to native SVG `text`/`tspan` conversion
- canonical export preparation
- SVG serialization
- SVG download
- PNG conversion

Production hardening requirements:

- Resolve the clicked card; never a global or neighbouring SVG.
- Snapshot the live camera, create canonical padded reset export, then restore the live camera.
- Strip scripts and event attributes.
- Namespace every ID and rewrite `url(#id)`, marker, mask, clip-path, and href references.
- Verify exported label nodes exist.
- Use the rendered viewport as canonical canvas: `viewBox="0 0 width height"`.
- Fill a background rectangle from `0,0` through the full viewBox using the active theme.
- Keep node fills and contrasting label colours visible.
- Preserve aspect ratio, centring, and four-sided padding.
- SVG width is the actual rendered viewport width; do not force 1024px.
- PNG is a direct rasterization of finalized SVG, width exactly 1023px, proportional height.
- Handle null PNG blobs and load/raster errors locally.
- Revoke all object URLs reliably.
- Export progress/failure status affects only its card.

### 7–9. Three independent production pilots

Each pilot is its own commit and retains its own legacy fallback.

1. **S03 Task Memory Packet**: nested architecture, real system boundaries, title padding, compact groups.
2. **F10 Evidence & Evaluation Loop**: explicit feedback/state cycle and correction path.
3. **S05 Agile Team Swimlane**: real actor lanes, artifact row, long-label handling, intentional wrapping.

Pilot content rules:

- Default fit communicates primary structure without zoom.
- No font shrinking to disguise congestion.
- Prefer width until the result becomes excessively wide; then use reviewed snake/stacked flow.
- Group only distinct systems, ownership, or runtime boundaries.
- Group padding is equal on all sides with extra top room for title.
- Use authored line breaks and content-aware wrapping.
- Put rationale/prose outside nodes.
- Retain adjacent text alternatives and input/output/internal-step meaning.

Do not begin bulk conversion until all three pass the full matrix.

### 10. Fast parity batch

Migrate S06, F01, F03, F05, and F08. Decide S02 reachability in a separate focused commit. These establish reusable simple-flow, lifecycle, and roadmap patterns.

### 11. Architecture batch

Migrate S01–S03 by shared architecture rules. Do not duplicate S03 work. Split S01 overview from detailed integrations where needed.

### 12. Workflow/state batch

Migrate F06, F07, F08, F10, F11, S04, and S05. S04 receives dedicated sequence validation rather than pretending the existing pseudo-sequence is a finished sequence diagram.

### 13. Data/dependency batch

Migrate F01–F05, S02 if retained, and S07. Separate measured facts, estimates, human review, outcomes, and calibration in S07/F04.

### 14. Explanatory cleanup

Migrate or simplify F00 and F09. Defer F12 until its ecosystem ownership/model stabilizes.

### 15. Remove legacy implementation

Only after all referenced contexts pass:

- Remove `node`, `flowDiagram`, and `systemDiagram`.
- Remove fixed `1040` viewBoxes, fixed 105/145px nodes, fixed edge-label rectangles, and `min-width:760/720px` rules.
- Remove legacy CSS/scripts only after information parity, export, theme, keyboard, resize, print, and rollback checks pass.
- Keep baseline screenshots and the pre-migration commit reference.

## Diagram information rules

- Architecture: five–nine systems, one visible container level, 24px-equivalent group padding, split beyond nine systems/two audiences.
- Components: seven–twelve nodes, 140–240px-equivalent node widths, 12–16px internal padding, split when crossings remain or nesting exceeds two levels.
- Data/request flow: five–nine steps, one dominant direction, verb edge labels under 24 characters, split beyond two feedback edges or three branches.
- Sequence: three–seven participants, 12–18 messages, time downward, notes for exceptional detail.
- State: five–ten states, top-to-bottom, distinct terminal states, explicit `event [guard] / action` semantics.
- Workflow/swimlane: four–six lanes, 12–16 actions, one owner per lane; artifacts are not actors.
- Dependency: ten–15 visible nodes, layered by depth, clusters only for ownership/runtime.
- Roadmap: five–eight phases, at most three parallel tracks, each phase carries one proof gate.
- Node titles stay concise; no paragraph prose in nodes.
- Edge labels appear only when relationship meaning is not obvious.
- Legends define notation and never repeat node content.
- Overview-to-detail navigation remains normal HTML.

## Content-preservation requirements

- Preserve the idea timeline: past observations, direction changes, present consolidation, future AI-SDLC path.
- Keep capabilities non-overlapping and interlinked through explicit related-capability references.
- Every capability states aim, practical company benefit, status, feasibility, evidence gate, immediate decision, and strongest limitation.
- Distinguish observed/external, proposed, and missing/unverified.
- Preserve serious papers and theories with provenance, applicability limits, and no claim that analogy proves engineering value.
- Every system/subsystem states inputs, outputs, internal steps, interfaces/owners, failure/repair path, evidence, and limitation.
- Embed team-level implementation and agile workflow implications in actionable prose.
- Preserve graceful degradation and evidence links to gateway/PMS without duplicating those products.

## Accessibility and fatigue gates

- Keyboard-first tabs, tree, popovers, toolbar, downloads, and theme/verbosity controls.
- Screen-reader names and state; visible focus; no trap.
- Status is never colour-only.
- Scalable typography, high contrast, reduced-motion support.
- Usable around 360×800 and 390×844.
- Adjacent text alternative for every diagram.
- One primary immediate action per decision context.
- Resume without repeated entry; errors and recovery appear next to failure.
- No essential content is tooltip-only.

## Validation loop per commit

Run the same narrow loop after every commit:

1. Desktop near 1440px.
2. Annotated constrained desktop 884×863.
3. Intermediate near 768px.
4. Mobile 390×844 and 360×800.
5. Light and dark themes.
6. Verbosity 0, 2, and 5.
7. Capability changes and every horizontal perspective.
8. Confirm one local panel, no document scroll/jump, and sidebar/content baseline match.
9. Keyboard tabs, tree, popovers, toolbar, dropdown, Escape, and focus visibility.
10. Zoom, pan, fit, reset, full/window, sidebar collapse, and resize.
11. Export SVG and PNG from two different diagrams; verify exact identity, labels, theme, full background, centring, padding, dimensions, and independent opening.
12. Switch views repeatedly; verify detached instances are destroyed.
13. Print preview and static/legacy fallback.
14. Zero duplicate SVG IDs, missing references, unhandled rejections, or console errors.
15. `git diff --check`, focused commit, SSH push.

## Completion definition

Migration is complete only when all 20 registry decisions are explicit; every retained definition passes content parity and the validation matrix; S02 is intentionally restored or removed; F12 is either migrated or explicitly deferred with boundary; the legacy generators are no longer referenced; exports match reset-state diagrams; and the atlas remains a maintainable standalone HTML document with reversible history.

## Fast decision statement

- Recommended rendering approach: Mermaid 11.15.0 for validated categories; Graphviz/Viz.js only for documented compound-layout failures.
- Recommended layout engine: Mermaid-native layouts, with ELK only where qualified; Graphviz dot for an approved fallback hard case.
- Recommended zoom/pan mechanism: one shared svg-pan-zoom 3.6.1 adapter and toolbar.
- Recommended SVG export mechanism: exact-card live-SVG clone, canonical padded reset, style freezing, native labels, ID rewriting, full-canvas theme background, and standalone serialization.
- CDN and version-pinning strategy: exact jsDelivr versions during migration; reviewed self-hosted assets before hardened release.
- Number of diagram libraries permitted: one initially, maximum two.
- Main technical risks: lifecycle leaks, source duplication, export reference loss, touch behavior, category-specific renderer variance, and information overload that layout cannot solve.
- Conditions before bulk migration: runtime, lifecycle, export hardening, and all three independent pilots pass the full matrix.
- First pilot diagrams: S03, F10, S05.
- Split/redesign before migration: F00, F04, F06, F07, F10–F12, S01, S03–S05, S07.
