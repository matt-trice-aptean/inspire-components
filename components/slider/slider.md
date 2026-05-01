---
component: Slider
id: slider
version: 0.1.0
status: draft
source-library: DevExtreme dxSlider
source-url: https://js.devexpress.com/Documentation/ApiReference/UI_Components/dxSlider/
figma-file: yck1tcUXgdQ5aYX6iUAwrO
figma-node: 22145-141089
figma-page: "ADK / Slider"
figma-collection: COMPONENTS
prototype: components/slider/slider.html
spec: components/slider/slider.md
modes:
  light: ADK
  dark: TDK
last-updated: 2026-05-01
---

# Slider

**Status:** Draft · **Modes:** ADK (Light) + TDK (Dark) · **Library:** DevExtreme dxSlider  
**Prototype:** `components/slider/slider.html` · **Figma:** [ADK / Slider — Draft](https://www.figma.com/design/yck1tcUXgdQ5aYX6iUAwrO/GE---Astronaut-Design-System?node-id=22145-141089)

> **Architecture:** The codebase is the source of truth. This spec is the contract. Figma is a visualization derived from it. Changes flow: spec → code + Figma (via agent). Never edit Figma token values by hand.

---

## Who reads what

| Section | Designer | Front End | Product | Agent |
|---|:---:|:---:|:---:|:---:|
| States | ✓ | ✓ | ✓ | ✓ |
| Color Tokens | ✓ | ✓ | — | ✓ |
| Dimension Tokens | — | ✓ | — | ✓ |
| HTML Structure | — | ✓ | — | ✓ |
| Variable Architecture | — | ✓ | — | ✓ |
| Acceptance Criteria — Visual | ✓ | — | ✓ | ✓ |
| Acceptance Criteria — Functional | — | ✓ | ✓ | ✓ |
| Acceptance Criteria — Figma Binding | ✓ | — | — | ✓ |
| Open Questions | ✓ | ✓ | ✓ | ✓ |

---

## States

Each state maps to a CSS trigger and a Figma variant property. Both must be kept in sync.

| id | Label | CSS Trigger | Figma Variant | Notes |
|---|---|---|---|---|
| `default` | Default | base styles (no class) | State=Default, Labels=False | Handle at 50% for prototype |
| `default-labels` | Default — With Labels | `.dx-slider-label-container` present | State=Default, Labels=True | Label container is a DOM sibling, not a state class |
| `hover` | Hover | `.dx-state-hover` on root (DevExtreme) or `:hover` on handle | State=Hover | DevExtreme applies at runtime |
| `focus` | Focus | `.dx-state-focused` on root (DevExtreme) or `:focus-visible` on handle | State=Focus | 2px dashed ring at handle edge |
| `active` | Active | `.dx-state-active` on handle (prototype) / `.dx-state-active` on root (DevExtreme) | State=Active | Inner stroke narrows from 4px → 3px |
| `disabled` | Disabled | `.dx-state-disabled` on root | State=Disabled, Labels=False | Removes all pointer + keyboard events |
| `disabled-labels` | Disabled — With Labels | `.dx-state-disabled` on root + label container present | State=Disabled, Labels=True | Labels inherit disabled text color |

**Out of scope for this version:**
- Tooltip variant (value bubble on drag) — define as a separate variant when needed
- Range slider (two-handle) — separate component
- Custom step sizes — DevExtreme `step` prop; no visual impact on this spec

---

## Color Tokens

Authoritative mapping: CSS variable → where it is applied → Figma binding → resolved values in both modes.

**Dark Mode column key:**
- `cascades` — the dark value arrives through the semantic token override in `html[data-theme="dark"]`. No explicit component var needed.
- `explicit: #value` — this component var must be set directly in `html[data-theme="dark"]`. Annotated `†` in the HTML prototype.

### Track

| CSS Variable | Selector → Property | Figma Variable | Collection | ADK Light | TDK Dark | Dark Mode |
|---|---|---|---|---|---|---|
| `--slider-track-bg` | `.dx-slider-track → background` | `Slider/Track/Default` | COMPONENTS | `#cfcece` · ADK 30 | `#50505d` · TDK 50 | cascades via `--color-text-disabled` |
| `--slider-track-bg-disabled` | `.dx-state-disabled .dx-slider-track → background` | `Slider/Track/Disabled` | COMPONENTS | `#e7e6e6` · ADK 20 | `#3c3c44` · TDK 40 | cascades via `--color-border-default` |

### Range

| CSS Variable | Selector → Property | Figma Variable | Collection | ADK Light | TDK Dark | Dark Mode |
|---|---|---|---|---|---|---|
| `--slider-range-bg` | `.dx-slider-range → background` | `Slider/Range/Default` | COMPONENTS | `#3f3e3d` · ADK 80 | `#d4d4d5` · TDK 80 | cascades via `--color-text-default` |
| `--slider-range-bg-disabled` | `.dx-state-disabled .dx-slider-range → background` | `Slider/Range/Disabled` | COMPONENTS | `#cfcece` · ADK 30 | `#50505d` · TDK 50 | cascades via `--color-text-disabled` |

### Handle

| CSS Variable | Selector → Property | Figma Variable | Collection | ADK Light | TDK Dark | Dark Mode |
|---|---|---|---|---|---|---|
| `--slider-handle-bg` | `.dx-slider-handle → background` | `Slider/Handle/Default` | COMPONENTS | `#3f3e3d` · ADK 80 | `#d4d4d5` · TDK 80 | cascades via `--color-text-default` |
| `--slider-handle-stroke` | `.dx-slider-handle → box-shadow (inset)` | `Slider/Handle Border/Default` | COMPONENTS | `#ffffff` · White | `#31313b` · TDK 30 | cascades via `--color-surface-default` |
| `--slider-handle-bg-hover` | `.dx-slider-handle:hover → background` | `Slider/Handle/Hover` | COMPONENTS | `#575655` · ADK 70 | `#bcbcbe` · TDK 70 | cascades via `--color-border-emphasis` |
| `--slider-handle-bg-focus` | `.dx-slider-handle:focus-visible → background` | `Slider/Handle/Focus` | COMPONENTS | `#575655` · ADK 70 | `#bcbcbe` · TDK 70 | cascades via `--color-border-emphasis` |
| `--slider-handle-ring-focus` | `.dx-slider-handle:focus-visible → outline` | `Border/Focus` | SHARED SEMANTIC | `#1a88b7` · ADK Inline Link | `#7c79ff` · TDK Inline Link | explicit: `#7c79ff` † |
| `--slider-handle-bg-active` | `.dx-slider-handle.dx-state-active → background` | `Slider/Handle/Active` | COMPONENTS | `#575655` · ADK 70 | `#bcbcbe` · TDK 70 | cascades via `--color-border-emphasis` |
| `--slider-handle-bg-disabled` | `.dx-state-disabled .dx-slider-handle → background` | `Slider/Handle/Disabled` | COMPONENTS | `#cfcece` · ADK 30 | `#31313b` · TDK 30 | explicit: `#31313b` † |
| `--slider-handle-stroke-disabled` | `.dx-state-disabled .dx-slider-handle → box-shadow (inset)` | `Slider/Handle Border/Disabled` | COMPONENTS | `#f2f2f2` · ADK 10 | `#50505d` · TDK 50 | explicit: `var(--color-text-disabled)` † |

### Label

Label elements bind to the SHARED SEMANTIC collection directly. No component-scoped Figma variable exists for label text color.

| CSS Variable | Selector → Property | Figma Variable | Collection | ADK Light | TDK Dark | Dark Mode |
|---|---|---|---|---|---|---|
| `--slider-label-text` | `.dx-slider-label → color` | `Text/Default` | SHARED SEMANTIC | `#3f3e3d` · ADK 80 | `#d4d4d5` · TDK 80 | cascades via `--color-text-default` |
| `--slider-label-text-disabled` | `.dx-state-disabled .dx-slider-label → color` | `Text/Disabled` | SHARED SEMANTIC | `#cfcece` · ADK 30 | `#50505d` · TDK 50 | cascades via `--color-text-disabled` |

---

## Dimension Tokens

Dimension tokens are not bound to Figma variables. Values are fixed across both modes unless noted.

| CSS Variable | Selector → Property | Value | Notes |
|---|---|---|---|
| `--slider-track-height` | `.dx-slider-track → height` | `4px` | — |
| `--slider-track-radius` | `.dx-slider-track → border-radius` | `2px` | Only non-zero radius in this component |
| `--slider-handle-size` | `.dx-slider-handle → width, height` | `16px` | — |
| `--slider-handle-stroke-width` | `.dx-slider-handle → box-shadow (inset spread)` | `4px` | — |
| `--slider-handle-stroke-width-active` | `.dx-slider-handle.dx-state-active → box-shadow (inset spread)` | `3px` | Narrows by 1px on drag |
| `--slider-handle-shadow` | `.dx-slider-handle → box-shadow (outer)` | `0 1px 4px rgba(0,0,0,0.18)` | Elevation/ADK Base. Removed on disabled. |
| `--slider-handle-ring-width` | `.dx-slider-handle:focus-visible → outline-width` | `2px` | — |
| `--slider-handle-ring-offset` | `.dx-slider-handle:focus-visible → outline-offset` | `0px` | Ring at handle edge, not outside |
| `--slider-label-font-size` | `.dx-slider-label → font-size` | `12px` | Inter Tight Regular |
| `--slider-label-line-height` | `.dx-slider-label → line-height` | `16px` | — |
| `--slider-label-gap` | `.dx-slider-wrapper → gap` | `0px` | Labels flush against track. Must be explicit — never omitted. |

---

## HTML Structure

Class names are fixed — they are emitted by DevExtreme. Do not invent or rename them.

```html
<!-- Default -->
<div class="dx-slider">
  <div class="dx-slider-wrapper">
    <div class="dx-trackbar-container">
      <div class="dx-slider-track">
        <div class="dx-slider-range"></div>
        <div class="dx-slider-handle" tabindex="0"></div>
      </div>
    </div>
  </div>
</div>

<!-- Default — With Labels (label container is a sibling of dx-trackbar-container) -->
<div class="dx-slider">
  <div class="dx-slider-wrapper">
    <div class="dx-trackbar-container">
      <div class="dx-slider-track">
        <div class="dx-slider-range"></div>
        <div class="dx-slider-handle" tabindex="0"></div>
      </div>
    </div>
    <div class="dx-slider-label-container">
      <span class="dx-slider-label">0</span>
      <span class="dx-slider-label">100</span>
    </div>
  </div>
</div>

<!-- Disabled — dx-state-disabled on root, no tabindex on handle -->
<div class="dx-slider dx-state-disabled">
  <!-- same inner structure -->
</div>
```

**DevExtreme runtime state classes** (applied by the library, not by hand):

| Class | Applied to | Trigger |
|---|---|---|
| `.dx-state-hover` | root `.dx-slider` | Mouse enter |
| `.dx-state-focused` | root `.dx-slider` | Keyboard focus |
| `.dx-state-active` | root `.dx-slider` | During drag |
| `.dx-state-disabled` | root `.dx-slider` | `disabled: true` prop |

---

## Variable Architecture

The slider is a **Tier 2 component-scoped variable exception**.

**Tier 1 (shared semantic tokens)** are used by default for all components — they cover surface, text, border, icon, brand, and status roles. The slider uses them for label colors.

**Tier 2 (component-scoped variables)** are required here because the slider has three unique visual roles — a muted track, a filled range, and a circular handle with an inner stroke — that have no direct equivalent in the shared semantic set. These roles cannot be mapped to `surface`, `text`, or `border` without creating misleading token semantics.

**Rules for this component:**
- All `--slider-*` variables in `:root` must reference semantic tokens — no raw hex.
- Raw hex is only permitted in `html[data-theme="dark"]` for values that cannot cascade from the semantic layer (marked `†`).
- If a future semantic token is added that covers one of these roles, migrate the component variable to reference it rather than keeping a raw value.

---

## Acceptance Criteria

### Visual (Designer · Agent)

Verify against `components/slider/slider.html`. Toggle dark mode with the button in the prototype header.

**Light mode (ADK):**
- [ ] Track: muted gray `#cfcece` (ADK 30)
- [ ] Range: dark `#3f3e3d` (ADK 80)
- [ ] Handle: solid dark circle `#3f3e3d`, 4px white inner stroke, ADK Base drop shadow
- [ ] Labels: `#3f3e3d`, Inter Tight 12px/16px, flush against track (0px gap)
- [ ] Hover: handle `#575655` (ADK 70)
- [ ] Focus: handle `#575655`, 2px dashed `#1a88b7` ring at handle edge (no offset)
- [ ] Active: handle `#575655`, inner stroke narrows to 3px
- [ ] Disabled track: `#e7e6e6` (ADK 20), range: `#cfcece`, handle: `#cfcece`, inner stroke: `#f2f2f2`, no shadow
- [ ] Disabled labels: `#cfcece`

**Dark mode (TDK):**
- [ ] Track: `#50505d` (TDK 50)
- [ ] Range: `#d4d4d5` (TDK 80)
- [ ] Handle: `#d4d4d5`, 4px `#31313b` inner stroke (TDK 30), same drop shadow
- [ ] Hover: handle `#bcbcbe` (TDK 70)
- [ ] Focus: handle `#bcbcbe`, 2px dashed `#7c79ff` ring at handle edge
- [ ] Active: handle `#bcbcbe`, inner stroke narrows to 3px
- [ ] Disabled track: `#3c3c44` (TDK 40), range: `#50505d`, handle: `#31313b`, inner stroke: `#50505d`, no shadow
- [ ] Page background and card backgrounds switch correctly (`#202026` page, `#31313b` card)

### Functional (Front End · Agent)

- [ ] Handle draggable via mouse (mousedown → mousemove → mouseup on document)
- [ ] Handle draggable via touch
- [ ] Click anywhere on track jumps handle to that position
- [ ] Arrow keys move ±1 step (0–100)
- [ ] Home key → 0, End key → 100
- [ ] `dx-state-disabled` on root blocks all pointer and keyboard events
- [ ] `cursor: not-allowed` visible on hover over any part of the disabled slider (track, range, handle, label area)
- [ ] No tabindex on handle when disabled

**Spacing verification:**
- [ ] `gap` on `.dx-slider-wrapper` resolves to `0px`
- [ ] No `margin-top` on `.dx-slider-label-container`
- [ ] Label row visually flush below handle zone in both states and both modes
- [ ] Spacing is identical between Default and Disabled with-label variants

### Figma Binding (Designer · Agent)

- [ ] All color fills in Figma frames are bound to Figma variables — no unbound hex values
- [ ] COMPONENTS collection variables exist for all 12 component-scoped roles listed in Color Tokens above
- [ ] Each COMPONENTS variable aliases a PRIMITIVES entry — no raw hex in Figma
- [ ] SHARED SEMANTIC collection used for `Text/Default` and `Text/Disabled` (label colors)
- [ ] Both ADK mode and TDK mode values set on every COMPONENTS variable
- [ ] Variable scopes set explicitly — not ALL_SCOPES
- [ ] `Border/Focus` (SHARED SEMANTIC) confirmed as `#1a88b7` ADK / `#7c79ff` TDK

---

## Open Questions

| # | Question | Owner | Status |
|---|---|---|---|
| 1 | Step size — currently fixed at 1 unit (0–100). Should `step` be configurable per instance? | Product | Open |
| 2 | Range slider (two-handle) — separate component or variant of this one? | Design | Open |
| 3 | Tooltip variant — value bubble on drag. Out of scope here; when needed, define as a separate variant with its own variable set | Design | Deferred |
