# Inspire Components — Claude Code Guide

## Available Skills

| Skill | Command | What it does |
|---|---|---|
| New Component | `/new-component` | Builds a new ADK/TDK design system component — HTML prototype + spec. |

Skills are CLI-only. They don't run in Claude Desktop or the Claude.ai web UI.

## Project Structure

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

## Key Conventions

- The spec (`.md`) is the contract. Code and Figma both reflect the spec.
- Semantic color tokens come from the Figma ADS file (`yck1tcUXgdQ5aYX6iUAwrO`), Variables page, **SHARED SEMANTIC** collection.
- Border radius is `0` everywhere (square corners). Exception: slider track uses `2px`.
- Font: `Inter Tight` for condensed/headings, `Inter` for body.
- Reference implementation: `components/slider/` is the canonical example.
