---
name: chisel
description: Design system chisel for frontend interfaces. Use when building UI components, starting a new frontend project, reviewing design consistency, tweaking design tokens, generating component showcases, or any visual/design system work. Triggers on "design system", "component showcase", "design tokens", "refine design", "audit design", "UI consistency", "start a frontend project", "change the palette", "update the design", /chisel, /cut, /trace, /lay, /trim, /check, "build page", "build component".
license: MIT
metadata:
  author: ppazosp
  version: "1.0.0"
---

# Chisel

Design system chisel for frontend interfaces. Cut the pattern, trace it onto pages, lay it out, trim the edges, check the work.

## Commands

| Command | What it does |
|---------|-------------|
| `/chisel cut` | Cut the chisel — detect context → aesthetic direction → [optional Stitch] → generate `system.md` + `tokens.css` + `showcase.html` → iterate |
| `/chisel trace` | Trace the pattern — build frontend pages using the design system, component by component |
| `/chisel lay` | Lay it out — regenerate component showcase from current `system.md` |
| `/chisel trim` | Trim the edges — describe change → update tokens → preview → approve → propagate |
| `/chisel check` | Hold it against the work — audit all frontend code against `system.md` → report → auto-fix |

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
/chisel cut   → cut the pattern → tokens → showcase → iterate
/chisel trace → trace the pattern → build pages component by component
/chisel lay   → lay out components from current system.md
/chisel trim  → trim the edges → preview → approve → propagate
/chisel check → hold against the work → report → auto-fix
```
