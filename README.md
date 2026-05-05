# Inspire Components

A design system component library for the ADK/TDK (GE Astronaut Design System). Each component ships as a self-contained HTML prototype and a machine-authoritative markdown spec.

## Structure

```
components/
  [id]/
    [id].html   — self-contained visual prototype with theme toggle
    [id].md     — machine-authoritative spec (source of truth)
  README.md     — component registry
.claude/
  skills/
    new-component/SKILL.md   — /new-component skill definition
```

## Component Registry

See [components/README.md](components/README.md) for the full registry, live demos, and spec links.

## Adding a Component

Use `/new-component` in Claude Code CLI. Provide a component name and a library docs URL.

```
/new-component
Component: Checkbox
Library URL: https://js.devexpress.com/Documentation/ApiReference/UI_Components/dxCheckBox/
```

> **CLI only.** Skills don't run in Claude Desktop or the Claude.ai web UI.

## Key Conventions

- The spec (`.md`) is the contract. Code and Figma both reflect the spec.
- Semantic color tokens come from the Figma ADS file (`yck1tcUXgdQ5aYX6iUAwrO`), Variables page, **SHARED SEMANTIC** collection.
- Border radius is `0` everywhere (square corners). Exception: slider track uses `2px`.
- Font: `Inter Tight` for condensed/headings, `Inter` for body.
- Reference implementation: `components/slider/` is the canonical example.

## Prerequisites

- [Claude Code CLI](https://www.npmjs.com/package/@anthropic-ai/claude-code): `npm install -g @anthropic-ai/claude-code`
- Figma MCP (for Figma writes and acceptance checks): `/mcp add figma` inside Claude Code
