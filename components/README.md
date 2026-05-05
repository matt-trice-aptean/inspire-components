# Component Registry

Each component lives in its own subdirectory: `components/[id]/`.

**Architecture:** The spec (`.md`) is the contract. The HTML prototype is a visual reference. Figma reflects the spec as the variable ecosystem is established and standardized.

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

Use `/new-component` in Claude Code CLI. Provide a component name and a library docs URL. The skill will:
1. Research the library's HTML class names and state classes
2. Confirm the state list with you
3. Build `components/[id]/[id].html` and `components/[id]/[id].md`
4. Optionally create Figma frames via the Figma MCP

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

> **CLI only.** Skills don't run in Claude Desktop or the Claude.ai web UI. See `.claude/skills/new-component/SKILL.md` for prerequisites and full guidance.

After adding a component, update the table above.

---

## Checking Component Compliance

Before a component is accepted into the registry, run the acceptance check. The agent performs 7 checks and produces a structured report for the reviewer (who may be the same designer submitting the component).

**What you need:**
- Claude Code CLI installed: `npm install -g @anthropic-ai/claude-code`
- Figma MCP connected: `/mcp add figma` inside Claude Code

**To check a single component**, give Claude the Figma node URL:
```
Run the acceptance check on this component: https://www.figma.com/design/[fileKey]/...?node-id=[nodeId]
```

**To check a full page**, give Claude the page URL:
```
Run the acceptance check on this page as a whole: https://www.figma.com/design/[fileKey]/...?node-id=[pageNodeId]
```

The agent will fetch variable definitions and design context directly from Figma, cross-reference all color tokens against the shared semantic token set, and output a pass/fail report.

**The 7 checks (all are blockers if failed):**
1. **Semantic variable compliance** — color tokens must use shared semantic vars where a direct replacement exists
2. **Spacing compliance** — gap and padding values must reference SPACING collection aliases
3. **Cross-component variable borrowing** — a component may only reference its own vars and shared semantic vars
4. **State completeness** — all states must be present or explicitly documented as out of scope
5. **Border radius compliance** — 0px everywhere; any deviation must be documented
6. **Dark mode completeness** — every color token must have a TDK value
7. **Token naming convention** — `--[component]-[element]-[property]-[state]`; generic palette aliases are not acceptable

> A component is not accepted until all ⛔ blockers are resolved. ⚠️ warnings flag drift or unconfirmed values that need a design decision.
