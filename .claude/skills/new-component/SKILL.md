---
name: new-component
description: Build a new ADK/TDK design system component. Minimum input is a component name and a source library URL (DevExtreme or other). Produces a self-contained HTML prototype and a machine-authoritative markdown spec. Optionally creates Figma frames. Requires Claude Code CLI — not available in the desktop app or web UI.
when_to_use: When a designer, product person, or developer wants to add a new component to the Edge One (ADK/TDK) design system using Claude Code CLI.
---

# ADK — New Component Skill

Runs the full new-component workflow for Edge One / Germanedge. Produces a visual HTML prototype and a machine-authoritative spec that serves as the contract between code and Figma.

**Architecture:** The spec is the contract. Figma reflects the spec as the variable ecosystem is established and standardized. Agents build from the spec in both directions — toward code and toward Figma.

**Reference implementation:** `components/slider/` — the slider is the canonical example of this format. Read `components/slider/slider.md` to understand the expected spec structure before building a new one.

---

## Prerequisites

| Requirement | Details |
|---|---|
| **Claude Code CLI** | Required. Skills run in the CLI only — they don't work in Claude Desktop or the Claude.ai web UI. Install: `npm install -g @anthropic-ai/claude-code` |
| **Figma MCP plugin** | Required only for Step 8 (Figma frame creation). Install via Claude Code: `/mcp add figma` |
| **GitHub account** | Optional. Needed only if you want a shareable live-demo URL (Step 7 explains setup for first-timers). |

---

## 1. Gather Inputs

| # | Input | Required | Notes |
|---|---|---|---|
| 1 | **Component name** | Yes | Used for directory name, file names, CSS variable prefix. E.g. `Checkbox`, `DatePicker` |
| 2 | **Library URL** | Yes | DevExtreme docs page or other source. Used to research HTML class names and state classes. |
| 3 | **States** | Infer | Derive from library docs and present to user for confirmation before building. |
| 4 | **Figma URL** | Optional | Existing Figma node to reference for variable values. If provided, use Figma MCP to extract bindings. |
| 5 | **Create Figma frames?** | Opt-in | Ask explicitly. If yes, use the `figma-use` skill after spec and prototype are complete. |

**Minimum invocation:**
```
/new-component
Component: Checkbox
Library URL: https://js.devexpress.com/Documentation/ApiReference/UI_Components/dxCheckBox/
```

**With all optional inputs:**
```
/new-component
Component: Checkbox
Library URL: https://js.devexpress.com/Documentation/ApiReference/UI_Components/dxCheckBox/
Figma URL: https://www.figma.com/design/yck1tcUXgdQ5aYX6iUAwrO/GE---Astronaut-Design-System?node-id=22145-141089
Create Figma frames? Yes
```

---

## 2. Research the Component

Fetch the library URL. Extract:

- Required HTML class names — fixed; the library's CSS targets them. Never invent class names.
- Runtime state classes (e.g. `.dx-state-hover`, `.dx-state-focused`, `.dx-state-active`, `.dx-state-disabled`)
- JavaScript required for interaction (drag, keyboard, touch)
- Default DOM hierarchy

---

## 3. Confirm States

Present the inferred state list to the user before building. For each state, identify:
- `id` — kebab-case key
- `label` — display name  
- `css-trigger` — how the state is activated
- `figma-variant` — Figma component variant property=value

Note out-of-scope variants explicitly — these go in the spec's Open Questions.

---

## 4. Plan the Variable Layer

For each visual property, assign a tier:

**Tier 1 — Shared semantic tokens (default).** Use when the role maps cleanly to: `--color-surface-*`, `--color-text-*`, `--color-border-*`, `--color-icon-*`, `--color-brand-*`, `--color-status-*`. The canonical token list lives in the Figma ADS file (`yck1tcUXgdQ5aYX6iUAwrO`), Variables page (`21891:8273`), **SHARED SEMANTIC** collection — use the Figma MCP to read the current variable names and values for both ADK (light) and TDK (dark) modes before writing any `:root` block.

**Tier 2 — Component-scoped variables (exception only).** Use when the component has unique visual roles with no semantic equivalent (e.g. a filled track, a circular handle, an inner stroke). Naming: `--[component]-[element]-[property]-[state]`.

**Rules:**
- Component vars in `:root` must reference semantic tokens — never raw hex.
- Raw hex only in `html[data-theme="dark"]` for values that cannot cascade from the semantic layer. Annotate these `†`.

---

## 5. Build the Spec

Create `components/[id]/[id].md`. Read `components/slider/slider.md` as the reference format.

### Required sections (in order)

**Frontmatter (YAML):**
```yaml
---
component: [Name]
id: [id]
version: 0.1.0
status: draft
source-library: [Library name]
source-url: [URL]
figma-file: [fileKey or "TBD"]
figma-node: [nodeId or "TBD"]
figma-page: "[Page name]"
figma-collection: COMPONENTS
prototype: components/[id]/[id].html
spec: components/[id]/[id].md
modes:
  light: ADK
  dark: TDK
last-updated: [YYYY-MM-DD]
---
```

**1. Who reads what** — compact table: Section × Audience (Designer / Front End / Product / Agent)

**2. States** — table: `id | Label | CSS Trigger | Figma Variant | Notes`. Plus explicit out-of-scope list.

**3. Color Tokens** — grouped by visual element (e.g. Track, Handle, Label). Each group is a table:

| CSS Variable | Selector → Property | Figma Variable | Collection | ADK Light | TDK Dark | Dark Mode |
|---|---|---|---|---|---|---|

- `Selector → Property` format: `.dx-element-name → css-property`
- ADK/TDK values: `#hexvalue · PrimitiveAlias` (e.g. `#cfcece · ADK 30`)
- Dark Mode column: `cascades via --semantic-token-name` OR `explicit: #value †`
- Figma Variable: exact path in the Figma collection (e.g. `Slider/Track/Default`). Never use CSS variable names here. If unknown, write `Unknown — requires Figma inspection`.
- Collection: `COMPONENTS` or `SHARED SEMANTIC`

**4. Dimension Tokens** — table: `CSS Variable | Selector → Property | Value | Figma Variable | Notes`.

Spacing variables live in the **SPACING collection** in the ADS Figma file. The collection has two tiers:
- **Primitives** — raw numeric values covering the full style guide scale. Not applied directly in design; they back the semantic layer.
- **Semantic (`Gap/` and `Padding/`)** — variables like `Gap/Gap-08`, `Padding/Padding-24`. Built on top of primitives but **not exhaustive** — only values that have been needed in production screens exist. Do not assume a semantic token exists for every primitive value.

**When populating the Figma Variable column:**
1. Check the SPACING collection for a `Gap/` or `Padding/` semantic variable matching the needed value. Use it if found (e.g. `Gap/Gap-08`).
2. If no semantic variable exists for that value, fall back to the primitive and note it: `SPACING primitive — no semantic alias yet`.
3. Never fabricate a semantic token name. If unknown, write `Unknown — requires Figma inspection`.

**5. HTML Structure** — code blocks for each structural variant. Table of DevExtreme runtime state classes.

**6. Variable Architecture** — one paragraph explaining the Tier 1/2 decision for this component and the rules that follow from it.

**7. Acceptance Criteria** — three subsections:
- Visual (Designer · Agent): per-state checklist for light and dark
- Functional (Front End · Agent): interaction and spacing checklist
- Figma Binding (Designer · Agent): variable binding completeness checklist

**8. Open Questions** — table: `# | Question | Owner | Status`

### Spec accuracy rules

- Never fabricate Figma variable names. If unknown, write `Unknown — requires Figma inspection`.
- Never use CSS variable names in the Figma Variable column. `--color-text-disabled` is a CSS implementation detail, not a Figma variable path.
- Primitive alias (e.g. `ADK 30`) is the PRIMITIVES entry the variable resolves to — only include if confirmed.
- Dark mode deviations are `†` with explicit values in the Dark Mode column.

---

## 6. Build the HTML Prototype

Create `components/[id]/[id].html`. Read `components/slider/slider.html` to calibrate the structure, then produce an equivalent for the new component.

### CSS structure (in order)

1. **Semantic tokens, light mode** — full `:root` block. Fetch current values from the Figma ADS file (`yck1tcUXgdQ5aYX6iUAwrO`), Variables page (`21891:8273`), **SHARED SEMANTIC** collection, ADK mode. Do not copy from slider.html — Figma is the source of truth and slider.html may be stale.
2. **Component variables** — `:root` block with `--[component]-*` vars referencing semantic tokens
3. **Dark mode overrides** — `html[data-theme="dark"]` block: semantic overrides + component explicit overrides (annotated `†`)
4. **Component CSS** — base structure, then one block per state

### Germanedge styling rules

- **Font:** `Inter Tight` for condensed / headings; `Inter` for body. Load from Google Fonts.
- **Border radius:** `0` — square corners everywhere. Exception: slider track uses `2px` (documented).
- **Focus rings:** `outline: 2px dashed [focus-color]; outline-offset: 0px` — at the element edge.
- **Disabled:** `cursor: not-allowed` on the root disabled element (e.g. `.dx-state-disabled`). Do not set `pointer-events: none` on interactive child elements — it prevents the cursor from displaying. Interaction is blocked via the JS guard (`if (slider.classList.contains('dx-state-disabled')) return;`) rather than pointer-events.
- **Spacing:** all gaps must be explicit CSS variables set to a value, never omitted.

### Page layout

```
body → background: var(--color-surface-subtle); padding: 40px 24px
  .page-wrap → max-width: 860px; margin: 0 auto
    page header → component name, subtitle, Figma link button (if URL available), theme toggle
    .state-group (one per state) → .state-label + component demo
    code reference section → four tabs: [Name]-spec.md | HTML | CSS Variables | Variable Reference
```

### Code reference tabs

**Tab order:** Spec tab is first and the default active tab on page load.

- **[Name]-spec.md** — the full embedded spec rendered as markdown via marked.js. Tab label and download filename are set dynamically from the `component` and `id` fields in the YAML frontmatter. Includes a "Download [id]-spec.md" link **right-aligned in the tab bar** (inline with the tab buttons) that triggers a file download of `[id].md` from the same directory.
- **HTML** — syntax-highlighted markup for each state variant
- **CSS Variables** — all component variables with semantic token mappings and dark mode overrides
- **Variable Reference** — table with 6 columns: `CSS Variable | Figma Variable | ADK value + alias | ADK swatch | TDK value + alias | TDK swatch`. One row group per visual element.

The spec tab serves every audience from a single shared URL — designers validate states, developers implement from the HTML and CSS tabs, agents consume the structured spec. No tab is hidden; the team uses what they need.

### Theme toggle

Button in page header that toggles `data-theme="dark"` on `<html>`. Label switches between "Dark Mode" and "Light Mode".

### Figma link button

Include only if a Figma URL was provided. Use the 5-shape Figma SVG mark (see `components/slider/slider.html` for the exact SVG).

---

## 7. Update the Registry

After both files are created, add a row to `components/README.md`. If the user has GitHub Pages set up, include a live demo link; otherwise use `—` for that column.

```md
| [Name] | `[id]` | Draft | [Figma]([figma-url]) | [Live demo](https://[github-username].github.io/inspire-components/components/[id]/[id].html) | [id].md]([id]/[id].md) |
```

### Live demo URL — optional but recommended

A live demo URL lets you share the prototype with anyone on the team via a plain link, no local setup needed. It requires GitHub Pages.

**If the user already has GitHub Pages set up**, use their URL pattern above and move on.

**If the user has no GitHub account**, walk them through the following. Do not assume any prior GitHub knowledge — offer to guide each step interactively if they want help.

1. **Create a GitHub account**
   Go to [github.com/signup](https://github.com/signup). Use your work email. Username will appear in the live URL (e.g. `jane-doe-aptean`).

2. **Fork the inspire-components repo**
   Go to the inspire-components repo on GitHub and click the **Fork** button (top right). This creates your own copy under your account.

3. **Enable GitHub Pages on your fork**
   In your fork, go to **Settings → Pages**. Under **Source**, choose **Deploy from a branch**, select the **main** branch and **/ (root)** folder, then click **Save**.

4. **Wait ~1 minute**, then your prototype is live at:
   `https://[your-username].github.io/inspire-components/components/[id]/[id].html`

5. **Push your new component files** to your fork's main branch. GitHub Pages will update automatically within a minute or two.

> If the user gets stuck at any step, offer to walk through it interactively. GitHub account creation, forking, and Pages setup are one-time tasks.

---

## 8. Figma Frame Creation (opt-in)

Only run if the user confirmed Figma creation at the start.

Load the `figma-use` skill, then:
1. Switch to or create the Figma page specified in the spec frontmatter
2. Build one auto-layout frame per state
3. Bind fills to Figma variables (COMPONENTS collection, ADK mode)
4. Add a header frame: component name, DRAFT badge, LIGHT MODE badge, state count
5. Screenshot and present for review

After Figma frames are created, update `figma-node` in the spec frontmatter with the new node ID.

### Figma naming conventions

There is an intentional split between how layers are named internally and how the final published component is named.

**Internal layers — use library class names.**
Name Figma layers after the source library's class names. This keeps a direct, traceable link between the Figma structure and the DOM. A developer reading the Figma file can immediately map a layer to the element it represents.

Example from the slider (DevExtreme):
- Frame layers: `dx-slider`, `dx-slider-wrapper`, `dx-trackbar-container`, `dx-slider-track`, `dx-slider-range`, `dx-slider-handle`, `dx-slider-label`

**Published component name — use Inspire design system naming.**
The top-level Figma component (the one that gets published to the library and used by designers) should use the Inspire/ADK name, not the library's internal name. The Inspire name is typically shorter, framework-agnostic, and matches what designers will search for.

Example: the DevExtreme `dxSlider` component is published in Figma as `Slider`.

**Why the split matters:**
- Designers consume the published component name — they don't need to know it's backed by DevExtreme
- Developers and agents trace layers back to the DOM using library class names
- If the underlying library ever changes, the published component name stays stable while the internal layer names update to match the new library

**In practice — when building Figma frames:**
1. Name all sublayers using library class names (e.g. `dx-slider-handle`)
2. Name the top-level component frame using the Inspire name (e.g. `Slider`)
3. Add a note in the Figma frame description: `Source: DevExtreme dxSlider` (or whichever library)
4. Document any name divergences in the spec's Open Questions if the final Inspire name hasn't been confirmed by design

---

## 9. Acceptance Check

Run this check after the spec and prototype are complete, before wrapping up. The agent performs all checks and produces a structured report for the human reviewer. The reviewer may be the same designer who submitted the component.

**All items marked ⛔ are blockers — the component cannot be accepted until they are resolved.**

---

### How to run the check

1. Read the component's Color Tokens table from the spec.
2. Cross-reference each token's ADK+TDK hex pair against the SHARED SEMANTIC set (see memory: `reference_csv_semantic_mapping.md` and `reference_semantic_consolidation_map.md`).
3. Check spacing values against the SPACING collection (see memory: `reference_figma_spacing.md`).
4. Inspect the HTML prototype and spec for cross-component variable borrowing, border radius deviations, missing dark mode values, and state completeness.
5. Output the report below.

---

### Report format

```
# Acceptance Check — [Component Name]
Date: [YYYY-MM-DD]
Spec: components/[id]/[id].md
Reviewer: [name or "self-review"]

---

## 1. Semantic Variable Compliance ⛔ BLOCKER if any row is FAIL

For each color token:
| Token | ADK hex | TDK hex | Status | Action |
|---|---|---|---|---|
| --[component]-[token] | #xxxxxx | #xxxxxx | ✅ PASS / ⛔ FAIL / ⚠️ DRIFT / — NO MATCH | [what to do] |

Status key:
  ✅ PASS      — already references a semantic token, or no semantic equivalent exists (gap)
  ⛔ FAIL      — a direct semantic replacement exists and is not being used (blocker)
  ⚠️ DRIFT     — ADK matches a semantic token but TDK value diverged (needs design decision)
  — NO MATCH  — no semantic equivalent exists yet (acceptable, flag for future semantic set expansion)

## 2. Spacing Compliance ⛔ BLOCKER if any value is hardcoded without a variable

| CSS Variable | Value | SPACING alias | Status |
|---|---|---|---|
| --[component]-[spacing-var] | 0px | Gap/Gap-08 or primitive | ✅ / ⛔ |

## 3. Cross-Component Variable Borrowing ⛔ BLOCKER if any found

List any instance where this component's CSS references a variable scoped to a different component (e.g. `--input-*` used inside a chat component).

  None found ✅
  — OR —
  ⛔ [variable name] — belongs to [other component], used in [this component element]

## 4. State Completeness

| State | Present | Notes |
|---|---|---|
| Default | ✅ / ⛔ | |
| Hover | ✅ / ⛔ / N/A | |
| Focus | ✅ / ⛔ / N/A | |
| Active | ✅ / ⛔ / N/A | |
| Disabled | ✅ / ⛔ / N/A | |
| Error | ✅ / ⛔ / N/A | |

Any state marked absent must be explicitly listed as out-of-scope in the spec's Open Questions section.

## 5. Border Radius Compliance ⛔ BLOCKER if deviation is undocumented

Global rule: 0px everywhere.
Documented exceptions: slider track (2px), chat list item (2px).

  Compliant ✅
  — OR —
  ⛔ [element] uses [value] — not documented as an exception

## 6. Dark Mode Completeness ⛔ BLOCKER if any token has no TDK value

All color tokens must have an explicit TDK value. Tokens with a blank or missing TDK entry are blockers.

  All tokens have TDK values ✅
  — OR —
  ⛔ [token name] — TDK value missing

## 7. Token Naming Convention

Component-scoped vars must follow: --[component]-[element]-[property]-[state]
Generic palette aliases (e.g. Generic/Theme/Theme 80, All Theme 50) are not acceptable as component token values — they must resolve through a semantic token or a properly named component var.

  Compliant ✅
  — OR —
  ⛔ [token name] — uses generic alias directly

---

## Summary

  PASS — ready for Figma binding and registry entry
  — OR —
  FAIL — [N] blockers must be resolved before acceptance (list them)
```

---

## 10. Wrap up

- Confirm output paths: `components/[id]/[id].html` and `components/[id]/[id].md`
- Note any `Unknown — requires Figma inspection` entries — these are designer action items
- Ask if they want to push to the repo
- Ask if Figma frames are needed (if not already done)
