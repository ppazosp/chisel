---
name: chisel
description: Design system studio for frontend interfaces. Use when building UI components, starting a new frontend project, reviewing design consistency, tweaking design tokens, generating component showcases, or any visual/design system work. Triggers on "design system", "component showcase", "design tokens", "refine design", "audit design", "UI consistency", "start a frontend project", "change the palette", "update the design", /chisel, /shape, /exhibit, /carve, /inspect.
license: MIT
metadata:
  author: ppazosp
  version: "1.0.0"
---

# Chisel

Design system studio for frontend interfaces. Shape direction, carve components, refine tokens, review consistency.

## Commands

| Command | What it does |
|---------|-------------|
| `/chisel shape` | Form the initial design — detect context → aesthetic direction → [optional Stitch] → generate `system.md` + `tokens.css` + `showcase.html` → iterate |
| `/chisel exhibit` | Display the work — regenerate component showcase from current `system.md` |
| `/chisel carve` | Precision adjustments — describe change → update tokens → preview → approve → propagate |
| `/chisel inspect` | Check for flaws — audit all frontend code against `system.md` → report → auto-fix |

## Always-On Skill

The `ui-craft` skill is always active when working on frontend code. It enforces micro-polish rules: animation decisions, surfaces, typography, gestures, and performance. See `skills/ui-craft/SKILL.md`.

## Agents

| Agent | Role |
|-------|------|
| Propagator | Applies design token changes across all component files, one component at a time |

## Design System Files

All design state lives in `docs/design/` at the project root:

```
docs/design/
├── system.md        # Source of truth — direction, tokens, component patterns, rationale
├── tokens.css       # Auto-generated CSS custom properties (never edit manually)
└── showcase.html    # Auto-generated component showcase (never edit manually)
```

## Workflow

```
/chisel shape   → form the initial design → tokens → showcase → iterate
/chisel exhibit → display components from current system.md
/chisel carve   → precision adjustments → preview → approve → propagate
/chisel inspect → check for flaws → report → auto-fix
```
