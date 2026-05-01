# ADK — New Component Workflow

A repeatable process for introducing new components into the Edge One (ADK) design system.
Each time this process is followed, the questions asked and decisions made should feed back
into refining these steps.

---

## Overview

```
1. Define  →  2. Research  →  3. Plan Variables  →  4. Build HTML/CSS
                                                          ↓
8. Sync HTML  ←  7. Iterate  ←  6. Bring to Figma  ←  5. Style
```

The guiding principle: **HTML/CSS is the source of structural truth; Figma is the source of visual truth.**
After the initial build, Figma edits flow back to HTML — not the other way around.

---

## Step 1 — Define the Component

Before writing any code or opening Figma:

- [ ] Name the component: `ADK / [Category] / [Name]` (e.g. `ADK / Slider`)
- [ ] List every state/variant to document (e.g. Default, With Labels, With Tooltip, Disabled)
- [ ] Identify the source library (DevExtreme, custom, etc.)
- [ ] Identify the primary audience for this artifact (designers, PO, front end devs)
- [ ] Note any async collaboration requirements (e.g. front end team on opposite timezone)

---

## Step 2 — Research the Framework Structure

For DevExtreme components:

- [ ] Find the component in the DevExtreme docs or source
- [ ] Document the required HTML class names (these are fixed — DevExtreme styles target them)
  - Example: `.dx-slider`, `.dx-slider-track`, `.dx-slider-handle`, `.dx-state-disabled`
- [ ] Document DevExtreme state classes: `.dx-state-hover`, `.dx-state-focused`,
  `.dx-state-active`, `.dx-state-disabled`
- [ ] Note any JavaScript needed for interaction behavior
- [ ] Note any SCSS override patterns from existing files (e.g. `dx-datagrid.scss`)

---

## Step 3 — Plan the Variable Layer

Determine which variable tier applies:

### Tier 1: Semantic tokens (default — use these first)
Map to existing semantic tokens where possible:

| Group    | Variables                                                       |
|----------|-----------------------------------------------------------------|
| Surface  | `--color-surface-default/subtle/hover/disabled/selected/…`     |
| Text     | `--color-text-default/subtle/disabled/inverse/…`               |
| Border   | `--color-border-default/subtle/strong/focus/…`                 |
| Icon     | `--color-icon-default/subtle/hover/disabled/…`                 |
| Brand    | `--color-brand-default/subtle/strong/hover/…`                  |
| Status   | `--color-status-success/warning/error/info` + `-surface`       |

### Tier 2: Component-scoped variables (exception — only when needed)
Use when the component has unique visual elements with no clean semantic equivalent
(e.g. slider track, range fill, handle states).

Naming convention: `--[component]-[element]-[property]-[state]`

Rules:
- Component variables **must still reference semantic tokens** — never raw hex values
- Document each component variable in the spec markdown
- Create matching Figma variables in the component's variable collection

**Decision checkpoint:** Could a semantic token cover this role? If yes, use it.
If no clean match exists, create a component-scoped variable.

---

## Step 4 — Build the HTML/CSS Implementation

Create `output/[component-name].html`.

### Structure
```
:root {
  /* === SEMANTIC LAYER — light mode === */
  /* surface, text, border, icon, status, brand */
}

:root {
  /* === COMPONENT VARIABLES — [ComponentName] === */
  /* --[component]-[element]-[property]: var(--color-...) */
}

/* Base/reset */
/* Component CSS using DevExtreme class names */
/* Per-state overrides */
```

### Checklist
- [ ] Semantic layer defined in `:root` (all groups)
- [ ] Component variable layer defined (if exception component)
- [ ] Each state rendered as a `.state-group` section with a `.state-label`
- [ ] DevExtreme class names used on all component elements
- [ ] Interaction JavaScript included (drag, keyboard, focus)
- [ ] Code reference section with three tabs:
  - **HTML** — syntax-highlighted HTML snippet per state
  - **CSS Variables** — all component variables with semantic mappings
  - **Variable Reference** — table: CSS Variable → Figma Variable → ADK hex + primitive alias → TDK hex + primitive alias

---

## Step 5 — Apply Germanedge Styling

- [ ] Font: `Inter Tight` substitutes for `GermanedgeSansCn` (condensed); `Inter` substitutes for `GermanedgeSans` (body) — GermanedgeSans is not available in this Figma file
- [ ] Border radius: `0` (square corners — per `e1-open-props-variables.scss: $border-radius: 0`)
- [ ] Neutrals: Tokyo palette (`tokyo-00` lightest → `tokyo-100` darkest)
- [ ] Brand/interactive: Brand palette (`brand-10` → `brand-160`); brand-100 = `#fcd515`
- [ ] Hover/active states use brand-strong (`#ab900e`) for border darkening
- [ ] Disabled states use surface-disabled (`#f2f2f2`) + border-default (`#e7e6e6`)
- [ ] Focus rings: `2px dashed --color-border-focus` with 3px offset
- [ ] Drop shadows via CSS `box-shadow` — not background color changes for elevation
- [ ] Sub-element spacing (gaps, margins between parts) must be explicit CSS variables — never rely on browser defaults or implicit gaps. Zero-gap relationships (e.g. label flush against track) must be set to `0px` explicitly, not omitted

---

## Variable Lookup Rule — Finding the Exact Figma Variable on a Node

When `get_design_context` returns an image asset instead of CSS with variable references
(common for flattened component layers), use this `use_figma` script to find the exact
variable and resolve its full alias chain:

```javascript
// Step 1 — find the fill variable binding on the target node
const node = page.findOne(n => n.id === 'NODE_ID');
const fills = node.fills.map(f => ({
  hex: f.type === 'SOLID'
    ? '#' + [f.color.r, f.color.g, f.color.b]
        .map(c => Math.round(c * 255).toString(16).padStart(2, '0')).join('')
    : null,
  variableId: f.boundVariables?.color?.id ?? null
}));
return fills;

// Step 2 — resolve the variable ID to name + collection
const variable = await figma.variables.getVariableByIdAsync('VariableID:xxxxx');
const collection = await figma.variables.getVariableCollectionByIdAsync(variable.variableCollectionId);
return { name: variable.name, collection: collection.name, modes: variable.valuesByMode };

// Step 3 — resolve alias chain (component var → semantic/primitive)
// Repeat getVariableByIdAsync on aliased IDs until you reach a raw COLOR value
```

**What to record once resolved:**
- Figma variable name (e.g. `Slider/Handle/Hover`)
- Collection name (e.g. `COMPONENTS`)
- Semantic/primitive alias per mode (e.g. ADK mode → `ADK/Theme Neutrals/ADK 70`)
- Resolved hex per mode (e.g. ADK: `#575655`, TDK: `#d4d4d5`)
- Matching CSS semantic token (e.g. `--color-border-emphasis`)

**Rule:** Never assume a color value from a visual approximation. If `get_design_context`
does not expose a variable name in its output, always run the Plugin API lookup above
before writing any CSS variable value.

---

## Step 6 — Bring to Figma

- [ ] Create a new Figma page: `ADK / [ComponentName] — Draft`
- [ ] Use the `figma-use` Figma MCP skill to build component frames programmatically
- [ ] Build one card per state, matching the HTML layout
- [ ] Add header frame with: component name, subtitle, DRAFT / LIGHT MODE / state-count tags
- [ ] Font substitution: use **Inter Tight** in place of GermanedgeSansCn; use **Inter** in place of GermanedgeSans

### Acceptance Criteria — Variable Alignment *(required before component is considered complete)*

- [ ] All color properties in Figma frames are **bound to Figma variables**
- [ ] Existing semantic tokens (`surface/`, `text/`, `border/`, `icon/`, `brand/`, `status/`) are
  used wherever they apply — do not duplicate a semantic token as a component variable
- [ ] Where a component-scoped variable is needed, create it in a **component variable collection**
  in Figma, named to match the CSS convention: `slider/track-bg`, `slider/handle-border`, etc.
- [ ] Component variables **alias** the semantic token — they do not hold raw color values
- [ ] All spacing relationships between sub-elements (gap, padding, margin) are defined as CSS variables and verified against the Figma component — pay special attention to zero-gap rules (e.g. label flush to track)
- [ ] Both **light mode** and **dark mode** values are set before the component is marked complete
- [ ] Variable scopes are explicitly set (e.g. `FRAME_FILL`, `SHAPE_FILL`, `TEXT_FILL`) —
  not left as `ALL_SCOPES`

---

## Step 7 — Iterate in Figma

The designer owns this phase:

- [ ] Review all states visually at intended viewport size
- [ ] Verify variable bindings resolve correctly in both modes
- [ ] Adjust values, spacing, or sizing as needed
- [ ] Note any changes that differ from the HTML implementation
- [ ] Annotate frames if handing off to front end asynchronously

---

## Step 8 — Sync Changes Back to HTML

After Figma iteration:

- [ ] Use `get_design_context` (Figma MCP) to extract updated values from edited frames
- [ ] Update CSS variable values in `output/[component-name].html`
- [ ] Update resolved hex values in the Variable Reference table
- [ ] Verify the HTML renders correctly after the update
- [ ] Note any Figma variable bindings that map to new semantic tokens not yet in the HTML

---

## Output Files per Component

| File                              | Purpose                                          |
|-----------------------------------|--------------------------------------------------|
| `output/[component].html`         | Living HTML/CSS implementation with code reference |
| `output/[component]-spec.md`      | Component spec: states, variables, acceptance criteria |
| `output/new-component-workflow.md`| This file — the repeatable process               |

---

## Iteration Pattern — Figma ↔ HTML

After the initial build, the workflow is not linear. Design and implementation trade corrections
back and forth across multiple rounds. Each round follows this micro-cycle:

```
Figma edit  →  inspect (Plugin API or get_design_context)  →  update HTML/CSS  →  update spec
```

### What triggers a sync

| Trigger | Action |
|---|---|
| Designer adjusts a variable value in Figma | Re-resolve with Plugin API → update CSS var + reference table |
| Designer adds dark mode TDK values | Fetch all component vars for TDK mode → add `html[data-theme="dark"]` block |
| Designer corrects a visual state (fill, stroke, ring) | Inspect that specific node → correct the specific CSS var + spec |
| A variable references an external/unknown source | Investigate origin → replace with equivalent from existing collections |
| Spec review reveals an incorrect assumption | Re-inspect the Figma node → correct and document what the approximation was |

### Dark mode sync pattern

Dark mode is added as a second pass after light mode is complete:

1. Designer sets TDK mode values in the COMPONENTS collection in Figma
2. Use the Plugin API to fetch all component variables with both ADK and TDK mode values in one call
3. For each variable, check whether the TDK value cascades correctly through the semantic token the CSS already references:
   - If it does → the dark mode semantic token override is sufficient; no component-level change needed
   - If it deviates → add an explicit override in `html[data-theme="dark"]` and mark it `†` in the reference table
4. Add a theme toggle button to the HTML demo so the dark mode can be verified interactively

### Checking if semantic cascade holds in dark mode

Before adding a direct component override, verify the semantic token's dark value:

```javascript
// Check what --color-text-disabled resolves to in TDK mode
const allVars = await figma.variables.getLocalVariablesAsync();
const v = allVars.find(v => v.name === 'Text/Disabled');
// walk alias chain for each mode — does TDK match what the component needs?
```

If the TDK value of the semantic token matches the TDK value of the component variable: no override needed.
If they differ: add the override and document why in a `†` footnote.

### Variable reference table format

The table columns are: **CSS Variable → Figma Variable → ADK value + primitive alias → TDK value + primitive alias**

- The "Figma Variable" column shows the name in the COMPONENTS (or SHARED SEMANTIC) collection — the variable the designer explicitly declared
- The alias annotation (e.g. `ADK 30`) is the PRIMITIVES entry the variable resolves to — this is the Figma-authoritative value, not a CSS implementation detail
- Do not list CSS semantic token names (e.g. `--color-text-disabled`) as a column — those are CSS implementation choices, not Figma declarations
- If a dark value deviates from the semantic cascade, annotate it `†` and explain in the footnote

### Correcting approximated values

If a value was initially approximated (e.g. from a visual color pick or `get_design_context` hex output)
and the exact Figma variable is later found, the correction propagates to four places:

1. `:root` CSS variable declaration in `output/[component].html`
2. `html[data-theme="dark"]` override block (if applicable)
3. CSS snippet tab in the code reference section
4. Variable reference table — Figma Variable, ADK value, TDK value, alias annotation
5. `output/[component]-spec.md` — handle variable table and acceptance criteria

### External variable references

If `get_design_context` or a Plugin API inspection reveals a variable from an external
library (e.g. Ant Design `Select/activeBorderColor`) that does not exist in the file's
own collections:

1. Do not hardcode the resolved hex — it may change and won't be maintainable
2. Search the existing collections for an equivalent that serves the same visual role
3. Work with the designer to remap the Figma variable to an internal one
4. Update the HTML to reference the new internal variable name

---

## Notes on This Process

- **Design values are authoritative** — dev SCSS files inform structure and naming, but Figma design values take precedence over SCSS values (dev styles may be inaccurate or in an unrelated theme mode)
- **The datagrid is a reference pattern** — `dx-datagrid.scss` shows how a complex DevExtreme component maps local SCSS variables to the theme palette; use it as a naming convention reference
- **Component-scoped variables are the exception** — most components should reference the semantic layer directly. Slider is an explicit exception because its visual parts (track, range, handle) have no clean semantic equivalent
- **Dark mode is a second-pass deliverable** — light mode is built and validated first; TDK mode values are added to Figma by the designer, then synced to the HTML using the dark mode sync pattern above
