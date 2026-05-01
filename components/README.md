# Component Registry

Each component lives in its own subdirectory: `components/[id]/`.

**Architecture:** The codebase is the source of truth. The spec (`.md`) is the contract. The HTML prototype is a visual reference. Figma is a visualization derived from the spec — it is never edited directly for token values.

**Files per component:**
- `[id].html` — self-contained visual prototype with theme toggle
- `[id].md` — machine-authoritative spec (frontmatter + token tables + acceptance criteria)

---

## Components

| Component | ID | Status | Live Demo | Spec | Figma |
|---|---|---|---|---|---|
| Slider | `slider` | Draft | [slider.html](https://matt-trice-aptean.github.io/inspire-components/components/slider/slider.html) | [slider.md](slider/slider.md) | [Figma](https://www.figma.com/design/yck1tcUXgdQ5aYX6iUAwrO/GE---Astronaut-Design-System?node-id=22145-141089) |

---

## Adding a Component

Use `/new-component` in Claude Code. Provide a component name and a library docs URL. The skill will:
1. Research the library's HTML class names and state classes
2. Confirm the state list with you
3. Build `components/[id]/[id].html` and `components/[id]/[id].md`
4. Optionally create Figma frames via the Figma MCP

After adding a component, update the table above.
