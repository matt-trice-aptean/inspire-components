---
name: new-component
description: Build a new ADK/TDK design system component. Minimum input is a component name and a source library URL (DevExtreme or other). Produces a self-contained HTML/CSS prototype and an audience-aware markdown spec. Optionally creates Figma frames.
when_to_use: When a designer, product person, or developer wants to document and prototype a new component for the Edge One (ADK/TDK) design system.
---

# ADK — New Component Skill

Runs the full ADK new-component workflow for Edge One / Germanedge. Produces a living HTML/CSS prototype and a structured markdown spec, following the patterns established by the ADK Slider component.

**Reference examples — read these at the start of every run:**
- `output/slider.html` — canonical prototype output
- `output/slider-spec.md` — canonical spec output

---

## 1. Gather Inputs

Ask for the following. Only 1 and 2 are required; infer or ask for the rest.

| # | Input | Required | Notes |
|---|---|---|---|
| 1 | **Component name** | Yes | Used for file names and CSS variable prefix. E.g. `Checkbox`, `DatePicker`, `Accordion` |
| 2 | **Library URL** | Yes | DevExtreme docs page or other source. Used to research HTML class names and state classes. |
| 3 | **States** | Infer | If not provided, derive from library docs and present to user for confirmation before proceeding. |
| 4 | **Figma URL** | Optional | Existing Figma node to reference for variable values. If provided, use Figma MCP to extract variable bindings. |
| 5 | **Create Figma frames?** | Opt-in | Ask explicitly. If yes, use the `figma-use` skill after the HTML and spec are complete. |

---

## 2. Research the Component

Fetch the library URL. Extract and document:

- Required HTML class names — these are fixed; the library's CSS targets them
- Runtime state classes (e.g. `.dx-state-hover`, `.dx-state-focused`, `.dx-state-active`, `.dx-state-disabled`)
- JavaScript required for interaction (drag, keyboard, touch)
- Default DOM hierarchy (wrapper → container → interactive element)

**Rule:** Never invent class names. Only use what the library documentation specifies.

---

## 3. Infer and Confirm States

Present the inferred state list to the user before proceeding. Typical pattern:

- Default
- Default — With [Variant] (e.g. labels, icon, placeholder)
- Hover
- Focus
- Active
- Disabled
- Disabled — With [Variant]
- Error (if applicable)

Note any states that are explicitly out of scope (e.g. tooltip variant, range variant) — these go in the spec's Open Questions.

---

## 4. Plan the Variable Layer

For each visual property, decide which tier applies. Ask: *could an existing semantic token cover this visual role?* If yes, use it. If no clean match exists, use a component-scoped variable.

### Tier 1 — Semantic Tokens (default)

| Group | Variables |
|---|---|
| Surface | `--color-surface-default/subtle/hover/disabled/selected` |
| Text | `--color-text-default/subtle/disabled/inverse/placeholder` |
| Border | `--color-border-default/subtle/strong/emphasis/focus` |
| Icon | `--color-icon-default/subtle/hover/disabled/inverse` |
| Brand | `--color-brand-default/subtle/strong/hover` |
| Status | `--color-status-success/warning/error/info` + `-surface` |

### Tier 2 — Component-Scoped Variables (exception only)

Use when the component has unique visual parts with no semantic equivalent (e.g. a track fill, a handle circle, a filled range).

Naming convention: `--[component]-[element]-[property]-[state]`
Example: `--slider-track-bg`, `--slider-handle-stroke-disabled`

**Rules:**
- Component variables must reference semantic tokens in `:root` — never raw hex values in the base block
- Raw hex is only permitted in `html[data-theme="dark"]` for values that deviate from the semantic cascade — annotate these `†`
- Document every component variable in the spec

---

## 5. Build the HTML Prototype

Create `output/[component-name].html` as a single self-contained file. Read `output/slider.html` to calibrate the exact expected structure, then produce an equivalent for the new component.

### CSS Layers (in order)

**Layer 1 — Semantic tokens, light mode:**
```css
:root {
  /* Surface */
  --color-surface-default:        #ffffff;
  --color-surface-subtle:         #fafafa;
  --color-surface-hover:          #e7e6e6;
  --color-surface-disabled:       #f2f2f2;   /* ADK 10 */
  --color-surface-selected:       #fffbe4;
  --color-surface-overlay:        #ffffff;

  /* Text */
  --color-text-default:           #3f3e3d;   /* ADK 80 */
  --color-text-subtle:            #6f6e6d;
  --color-text-disabled:          #cfcece;   /* ADK 30 */
  --color-text-placeholder:       #cfcece;
  --color-text-inverse:           #ffffff;

  /* Border */
  --color-border-default:         #e7e6e6;   /* ADK 20 */
  --color-border-subtle:          #fafafa;
  --color-border-strong:          #3f3e3d;   /* ADK 80 */
  --color-border-emphasis:        #575655;   /* ADK 70 */
  --color-border-focus:           #878686;   /* ADK 60 */

  /* Icon */
  --color-icon-default:           #878686;
  --color-icon-subtle:            #cfcece;
  --color-icon-hover:             #3f3e3d;
  --color-icon-disabled:          #cfcece;
  --color-icon-inverse:           #ffffff;

  /* Status */
  --color-status-success:         #8fda34;
  --color-status-success-surface: #f0fad9;
  --color-status-warning:         #ffb23f;
  --color-status-warning-surface: #fff4e0;
  --color-status-error:           #e53e3e;
  --color-status-error-surface:   #fef2f2;
  --color-status-info:            #3b82f6;
  --color-status-info-surface:    #eff6ff;

  /* Brand */
  --color-brand-default:          #fcd515;
  --color-brand-subtle:           #fae164;
  --color-brand-strong:           #ab900e;
  --color-brand-hover:            #fbe67e;
}
```

**Layer 2 — Component variables (if Tier 2 applies):**
```css
:root {
  --[component]-[element]-[property]: var(--color-...);
}
```

**Layer 3 — Dark mode overrides:**
```css
html[data-theme="dark"] {
  --color-surface-subtle:    #202026;   /* TDK 20 */
  --color-surface-default:   #31313b;   /* TDK 30 */
  --color-surface-disabled:  #3c3c44;   /* TDK 40 */
  --color-surface-hover:     #3c3c44;
  --color-surface-overlay:   #31313b;
  --color-text-default:      #d4d4d5;   /* TDK 80 */
  --color-text-subtle:       #bcbcbe;   /* TDK 70 */
  --color-text-disabled:     #50505d;   /* TDK 50 */
  --color-text-placeholder:  #50505d;
  --color-text-inverse:      #202026;
  --color-border-default:    #3c3c44;   /* TDK 40 */
  --color-border-emphasis:   #bcbcbe;   /* TDK 70 */
  /* Add component overrides for vars that don't cascade correctly — mark † */
}
```

### Germanedge Styling Rules

- **Font:** `Inter Tight` for condensed (substitute for GermanedgeSansCn); `Inter` for body text (substitute for GermanedgeSans). Load both from Google Fonts.
- **Border radius:** `0` — square corners per `$border-radius: 0`
- **Focus rings:** `outline: 2px dashed var(--color-border-focus); outline-offset: 0px` — at the element edge. For interactive components with a dedicated focus color (e.g. inline link blue), use a component-scoped variable.
- **Shadows:** `box-shadow` only — not background changes for elevation
- **Spacing:** All sub-element gaps, margins, and padding must be explicit CSS variables. Zero-gap relationships must be set to `0px` explicitly — never omitted.
- **Disabled:** `pointer-events: none; cursor: not-allowed;`

### Page Layout

```
body → background: var(--color-surface-subtle); padding: 40px 24px
  .page-wrap → max-width: 860px; margin: 0 auto
    .page-header → component title, subtitle, theme toggle button, optional Figma link
    .state-group (one per state) → .state-label + component demo
    .code-reference → three tabs: HTML | CSS Variables | Variable Reference
```

### Code Reference Section

Three tabs:
- **HTML** — syntax-highlighted snippet for each state variant
- **CSS Variables** — all component variables with semantic token mappings
- **Variable Reference** — table with 6 columns:

  `CSS Variable | Figma Variable | ADK value + alias | ADK swatch | TDK value + alias | TDK swatch`

  - Value cells: `#cfcece <span class="alias">ADK 30</span>`
  - Swatches: 12×12px inline-block colored div
  - Figma Variable column: use collection prefix (`Slider/Track/Default`) or note shared semantic (`Text/Default (SHARED SEMANTIC)`)
  - TDK values that deviate from cascade: annotate `†`, explain in footnote below the table

### Theme Toggle

Include a button in the page header that toggles `data-theme="dark"` on `<html>`. Label switches between "Dark Mode" and "Light Mode".

### Figma Link Button (if Figma URL was provided)

Include the official Figma mark (5-shape SVG) as a link button next to the theme toggle, linking to the Figma URL. See `output/slider.html` for the exact SVG markup.

---

## 6. Build the Spec

Create `output/[component-name]-spec.md`. Read `output/slider-spec.md` to calibrate the structure, then produce an equivalent for the new component.

### Required Sections (in order)

1. **Header block** — status (Draft), modes (Light + Dark), source library, HTML prototype path, Figma URL (if available)
2. **Reading Guide** — matrix: Section × Audience (Designer / Front End / Product / AI Agent)
3. **States** — table: State | Description. Note out-of-scope variants.
4. **Design Acceptance Criteria** — checkbox lists for light mode and dark mode, plus Typography
5. **HTML Structure** — code blocks for each structural variant, list of runtime state classes
6. **Variable Rationale** — explain Tier 1 vs Tier 2 decision for this component
7. **Component Variables** — tables per visual group (Track, Range, Handle, Label, etc.): `CSS Variable | Figma Variable | ADK (Light) | TDK (Dark)`. Include primitive alias inline: `#cfcece · ADK 30`
8. **Functional Acceptance Criteria** — interaction checklist (mouse, touch, keyboard, disabled) + spacing verification
9. **Variable Alignment Checklist** — designer checklist for Figma binding completeness
10. **Open Questions** — unresolved items for product/design/dev

### Spec Accuracy Rules

- **Never fabricate Figma variable names.** If unknown, write `Unknown — requires Figma inspection`.
- **Never list CSS semantic token names as Figma variables.** `--color-text-disabled` is a CSS implementation choice, not a Figma declaration. The Figma Variable column holds the Figma collection path only.
- **Primitive alias** (e.g. `ADK 30`) is the PRIMITIVES entry the variable resolves to — only include if confirmed via Plugin API or Figma inspection.
- **Dark mode deviations** are marked `†` with a footnote explaining why the value doesn't cascade from the semantic token.
- **No fabricated semantic token names** in the Variable Rationale — only reference tokens that exist in the semantic layer.

---

## 7. Figma Frame Creation (opt-in)

Only run this step if the user confirmed Figma creation at the start.

Load the `figma-use` skill. Then:

1. Switch to or create a Figma page named `ADK / [ComponentName] — Draft`
2. Build one auto-layout card per state, matching the HTML layout
3. Apply fill colors using the correct Figma variable bindings (COMPONENTS or SHARED SEMANTIC collection)
4. Add a header frame: component name, subtitle, DRAFT tag, LIGHT MODE tag, state count
5. Font: Inter Tight (condensed), Inter (body)
6. After building, take a screenshot and present it for review

**Note:** Variable bindings require that the Figma file has the COMPONENTS, SHARED SEMANTIC, and PRIMITIVES collections. If running against a different Figma file, check for these collections first.

---

## 8. After Both Files Are Complete

- Confirm output file paths with the user
- Ask if they want to push to the GitHub repo
- Ask if they want to create Figma frames (if not already done)
- Note any Figma variable names that were left as `Unknown — requires Figma inspection` — these are designer action items

Do not modify `output/new-component-workflow.md` unless the workflow process itself has changed based on something new discovered during this run.
