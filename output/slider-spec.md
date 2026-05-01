# ADK / Slider — Component Spec

**Status:** Draft — ready for front end review and designer validation  
**Modes:** Light (ADK) + Dark (TDK)  
**Source library:** DevExtreme  
**HTML prototype:** `output/slider.html`  
**Figma:** [ADK / Slider — Draft](https://www.figma.com/design/yck1tcUXgdQ5aYX6iUAwrO/GE---Astronaut-Design-System?node-id=22145-141089)

---

## Reading Guide

This spec serves three audiences. Read the sections marked for you; skip the rest.

| Section | Designer | Front End | Product | AI Agent |
|---|:---:|:---:|:---:|:---:|
| States | ✓ | ✓ | ✓ | ✓ |
| Design Acceptance Criteria | ✓ | — | ✓ | ✓ |
| HTML Structure | — | ✓ | — | ✓ |
| Variable Rationale | — | ✓ | — | ✓ |
| Component Variables | ✓ | ✓ | — | ✓ |
| Functional Acceptance Criteria | — | ✓ | ✓ | ✓ |
| Variable Alignment Checklist | ✓ | — | — | ✓ |
| Open Questions | ✓ | ✓ | ✓ | ✓ |

**Designers:** Use the HTML prototype to validate visual states against Figma. Open the file in a browser, toggle between light and dark, and check each state against the Design Acceptance Criteria below.

**Front end developers:** The HTML prototype is a living spec — it shows the expected structure, CSS variables, and interaction behavior. Use it as a reference implementation, not production code. DevExtreme will manage state classes at runtime; your task is to match the visual output and wire up the variable layer.

**Product:** Review the States section and Design Acceptance Criteria to validate scope and behavior. Flag any missing states or interaction requirements in Open Questions.

**AI agents:** All sections are relevant. The Component Variables tables are the authoritative mapping between Figma variables and CSS. Always resolve values from those tables — do not approximate from visual inspection.

---

## States

*For: Everyone*

| State | Description |
|---|---|
| Default | Draggable slider, no labels |
| Default — With Labels | Draggable slider with min/max labels below the track |
| Disabled | Non-interactive; all colors muted, cursor not-allowed |
| Disabled — With Labels | Disabled with muted min/max labels |

**Out of scope for this draft:**
- Tooltip variant (value bubble on handle) — not part of the ADK slider component
- Range slider (two handles) — separate component or variant
- Custom step sizes — currently fixed at 1 unit (0–100); configurable via DevExtreme `step` prop

---

## Design Acceptance Criteria

*For: Designer, Product*

Open `output/slider.html` in a browser. Toggle Dark Mode using the button in the top-right corner. Check each state against the items below.

### Light mode (ADK)

- [ ] Default: track is muted gray (`#cfcece`), filled range is dark (`#3f3e3d`), handle is a solid dark circle with a 4px white inner stroke and a soft drop shadow
- [ ] With Labels: min/max labels (`0` / `100`) appear flush below the track — no visible gap
- [ ] Hover: handle darkens slightly to ADK 70 (`#575655`)
- [ ] Focus (tab to handle): handle darkens to ADK 70; a 2px dashed teal ring (`#1a88b7`) appears at the handle edge
- [ ] Active (click and drag): handle stays ADK 70; inner stroke narrows from 4px to 3px during drag
- [ ] Disabled: track lightens to `#e7e6e6`, range and handle become `#cfcece` — no shadow, no interaction
- [ ] Disabled labels are muted (`#cfcece`) — same tone as disabled handle

### Dark mode (TDK)

- [ ] Default: track is dark (`#50505d`), filled range is light (`#d4d4d5`), handle is a light circle with a 4px dark inner stroke (`#31313b`) and drop shadow
- [ ] Hover: handle lightens slightly to TDK 70 (`#bcbcbe`)
- [ ] Focus: handle lightens to TDK 70; a 2px dashed purple ring (`#7c79ff`) appears at the handle edge
- [ ] Active: inner stroke narrows from 4px to 3px during drag
- [ ] Disabled: track `#3c3c44`, range `#50505d`, handle `#31313b`, inner stroke `#50505d` — no shadow

### Typography

- [ ] Labels use Inter Tight Regular, 12px / 16px line-height
- [ ] Disabled labels are muted in both modes

---

## HTML Structure

*For: Front End, AI Agent*

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

<!-- With Labels — label container is a sibling inside dx-slider-wrapper -->
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

<!-- Disabled — add dx-state-disabled to root, remove tabindex from handle -->
<div class="dx-slider dx-state-disabled">
  <!-- same inner structure, no tabindex on handle -->
</div>
```

**DevExtreme state classes applied at runtime:**
- `.dx-state-hover` — applied by DevExtreme on mouse enter
- `.dx-state-focused` — applied by DevExtreme on keyboard focus
- `.dx-state-active` — applied by DevExtreme during drag
- `.dx-state-disabled` — set on the root element to disable the component

---

## Variable Rationale

*For: Front End, AI Agent*

The slider is a **component-scoped variable exception**. Its visual parts — track, filled range, and circular handle — have no direct match in the shared semantic token set. Component variables are used so the semantic layer remains untouched and the slider's unique visual roles are explicit.

**Rule:** All `--slider-*` variables must reference semantic tokens or be explicitly overridden in `html[data-theme="dark"]`. No raw hex values in the base `:root` block.

---

## Component Variables

*For: Designer (Figma alignment), Front End (implementation), AI Agent*

All values sourced from the Figma COMPONENTS collection via Plugin API. Primitive alias (e.g. `ADK 30`) is the PRIMITIVES entry the Figma variable resolves to.

### Track

| CSS Variable | Figma Variable | ADK (Light) | TDK (Dark) |
|---|---|---|---|
| `--slider-track-bg` | `Slider/Track/Default` | #cfcece · ADK 30 | #50505d · TDK 50 |
| `--slider-track-bg-disabled` | `Slider/Track/Disabled` | #e7e6e6 · ADK 20 | #3c3c44 · TDK 40 |
| `--slider-track-height` | — | 4px | 4px |
| `--slider-track-radius` | — | 2px | 2px |

### Range

| CSS Variable | Figma Variable | ADK (Light) | TDK (Dark) |
|---|---|---|---|
| `--slider-range-bg` | `Slider/Range/Default` | #3f3e3d · ADK 80 | #d4d4d5 · TDK 80 |
| `--slider-range-bg-disabled` | `Slider/Range/Disabled` | #cfcece · ADK 30 | #50505d · TDK 50 |

### Handle

| CSS Variable | Figma Variable | ADK (Light) | TDK (Dark) |
|---|---|---|---|
| `--slider-handle-size` | — | 16px | 16px |
| `--slider-handle-bg` | `Slider/Handle/Default` | #3f3e3d · ADK 80 | #d4d4d5 · TDK 80 |
| `--slider-handle-stroke` | `Slider/Handle Border/Default` | #ffffff · White | #31313b · TDK 30 |
| `--slider-handle-stroke-width` | — | 4px | 4px |
| `--slider-handle-stroke-width-active` | — | 3px (narrows on drag) | 3px |
| `--slider-handle-shadow` | — | 0 1px 4px rgba(0,0,0,0.18) | same |
| `--slider-handle-bg-hover` | `Slider/Handle/Hover` | #575655 · ADK 70 | #bcbcbe · TDK 70 |
| `--slider-handle-bg-focus` | `Slider/Handle/Focus` | #575655 · ADK 70 | #bcbcbe · TDK 70 |
| `--slider-handle-ring-focus` | `Border/Focus` (SHARED SEMANTIC) | #1a88b7 · ADK Inline Link | #7c79ff · TDK Inline Link |
| `--slider-handle-ring-width` | — | 2px | 2px |
| `--slider-handle-ring-offset` | — | 0px | 0px |
| `--slider-handle-bg-active` | `Slider/Handle/Active` | #575655 · ADK 70 | #bcbcbe · TDK 70 |
| `--slider-handle-bg-disabled` | `Slider/Handle/Disabled` | #cfcece · ADK 30 | #31313b · TDK 30 † |
| `--slider-handle-stroke-disabled` | `Slider/Handle Border/Disabled` | #f2f2f2 · ADK 10 | #50505d · TDK 50 † |

### Label

Labels use the SHARED SEMANTIC collection directly — no component-scoped Figma variable exists for label text.

| CSS Variable | Figma Variable | ADK (Light) | TDK (Dark) |
|---|---|---|---|
| `--slider-label-text` | `Text/Default` (SHARED SEMANTIC) | #3f3e3d · ADK 80 | #d4d4d5 · TDK 80 |
| `--slider-label-text-disabled` | `Text/Disabled` (SHARED SEMANTIC) | #cfcece · ADK 30 | #50505d · TDK 50 |
| `--slider-label-font-size` | — | 12px | 12px |
| `--slider-label-line-height` | — | 16px | 16px |

### Spacing

| CSS Variable | Value | Rule |
|---|---|---|
| `--slider-label-gap` | 0px | Label container sits flush against the track — no gap |

† These dark mode values do not cascade from the same semantic token as ADK. They are overridden directly in `html[data-theme="dark"]`.

---

## Functional Acceptance Criteria

*For: Front End, Product*

- [ ] Handle is draggable via mouse (mousedown → mousemove → mouseup)
- [ ] Handle is draggable via touch
- [ ] Clicking anywhere on the track jumps the handle to that position
- [ ] Keyboard: Arrow keys move ±1 step; Home = 0; End = 100
- [ ] Disabled state ignores all pointer and keyboard events
- [ ] `dx-state-disabled` on the root element is sufficient to disable all interaction

**Spacing verification:**
- [ ] `gap` on `.dx-slider-wrapper` is `var(--slider-label-gap)` = 0px
- [ ] No `margin-top` on `.dx-slider-label-container`
- [ ] Label row aligns directly below the handle zone with no additional space
- [ ] Label spacing is consistent between Default and Disabled states

---

## Variable Alignment Checklist

*For: Designer — required before component is marked complete*

- [ ] All color properties in Figma frames are bound to Figma variables — no unbound fills
- [ ] Existing semantic tokens (`Surface/`, `Text/`, `Border/`, `Icon/`, `Brand/`) used wherever they apply
- [ ] Component-scoped COMPONENTS variables exist for all slider-specific roles:
  `Slider/Track/Default`, `Slider/Track/Disabled`, `Slider/Range/Default`, `Slider/Range/Disabled`,
  `Slider/Handle/Default`, `Slider/Handle/Hover`, `Slider/Handle/Focus`, `Slider/Handle/Active`,
  `Slider/Handle/Disabled`, `Slider/Handle Border/Default`, `Slider/Handle Border/Hover`,
  `Slider/Handle Border/Focus`, `Slider/Handle Border/Active`, `Slider/Handle Border/Disabled`
- [ ] Each component variable aliases a semantic or primitive token — no raw hex values in Figma
- [ ] Variable scopes explicitly set (not ALL_SCOPES)
- [ ] Both ADK and TDK mode values set on all component variables
- [ ] `Border/Focus` (SHARED SEMANTIC) used for focus ring — confirmed as `#1a88b7` ADK / `#7c79ff` TDK

---

## Open Questions

- **Step size** — currently fixed at 1 unit (0–100). DevExtreme `step` prop should make this configurable; confirm expected values with product
- **Range slider** — two-handle variant is out of scope for this draft; track as a separate component when needed
- **Tooltip variant** — not part of ADK slider; if needed in future, define as a separate variant with its own variable set
