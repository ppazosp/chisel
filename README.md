<p align="center">
  <img src=".github/chisel.png" alt="Stencil" width="200">
</p>

<h1 align="center">Stencil</h1>

<p align="center">
  Design system stencil for frontend interfaces.
</p>

<p align="center">
  <a href="https://github.com/ppazosp/stencil/blob/main/LICENSE"><img src="https://img.shields.io/github/license/ppazosp/stencil" alt="License"></a>
  <a href="https://github.com/ppazosp/stencil/releases"><img src="https://img.shields.io/github/v/release/ppazosp/stencil?include_prereleases" alt="Release"></a>
  <a href="https://skills.sh/ppazosp/stencil"><img src="https://img.shields.io/badge/agent_skill-stencil-blue" alt="Agent Skill"></a>
  <a href="https://agentskills.io"><img src="https://img.shields.io/badge/works_with-18%2B_agents-green" alt="Works with 18+ agents"></a>
</p>

<p align="center">
  Cut the pattern. Lay it out. Trim the edges. Check the work.<br>
  All state in markdown. Zero external services.
</p>

---

## Install

### Any Agent (recommended)

```bash
npx skills add ppazosp/stencil

# update later
npx skills update
```

This installs Stencil to `~/.agents/skills/stencil/` where it's automatically discovered by Claude Code, Codex, Copilot, Cursor, Gemini CLI, OpenCode, and [18+ other agents](https://agentskills.io).

### Claude Code (plugin mode)

Install as a plugin to get `/stencil:cut`, `/stencil:trim`, etc. in the autocomplete menu:

```
/plugin marketplace add ppazosp/backpack
/plugin install stencil@backpack
```

Or add to `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "backpack": {
      "source": { "source": "github", "repo": "ppazosp/backpack" }
    }
  },
  "enabledPlugins": {
    "stencil@backpack": true
  }
}
```

### Manual

Clone into your project and point your agent's instruction file (`AGENTS.md`, `.github/copilot-instructions.md`, etc.) to the `AGENTS.md` file in this repo.

## Quick Start

```bash
# 1. Cut: establish design direction, generate tokens + showcase
/stencil cut "admin dashboard"

# 2. Lay: display the component showcase
/stencil lay

# 3. Trim: tweak tokens, preview, propagate
/stencil trim "rounder buttons, warmer palette"

# 4. Check: audit code against the design system
/stencil check
```

## Commands

| Command | What it does |
|---------|-------------|
| `/stencil cut` | Cut the stencil — detect context → aesthetic direction → research style → [optional Stitch] → generate tokens + showcase → iterate |
| `/stencil lay` | Lay it out — regenerate the component showcase from current `system.md` |
| `/stencil trim` | Trim the edges — tweak tokens → regenerate showcase → approve → propagate to all components in parallel |
| `/stencil check` | Hold against the work — audit all frontend code against `system.md` → report violations → auto-fix |

## Agents

Stencil includes 1 subagent prompt in `agents/`. It's dispatched during `/stencil trim` to propagate token changes.

| Agent | Role | File |
|-------|------|------|
| Propagator | Applies design token changes across component files, one file per agent in parallel | `agents/propagator.md` |

On platforms that support subagents (Claude Code, Codex), multiple propagators are dispatched in parallel — one per component file. On platforms without subagent support, the main agent reads the prompt and follows its instructions inline.

## Always-On Skill: UI Craft

Stencil includes `ui-craft`, a passive skill that's always active when working on frontend code. It enforces micro-polish rules across 5 areas:

| Reference | What it covers |
|-----------|---------------|
| `animation.md` | Animation decision framework, easing, timing, springs, stagger, clip-path, enter/exit patterns |
| `surfaces.md` | Concentric border radius, optical alignment, shadow-as-border, image outlines, hit areas |
| `typography.md` | Font smoothing, tabular numbers, text-wrap balance/pretty |
| `gestures.md` | Drag interactions, momentum dismissal, damping, pointer capture, friction |
| `performance.md` | GPU compositing, `will-change`, CSS vs JS animations, Framer Motion caveats |

## How It Works

### Design system files

All design state lives in `docs/design/` at the project root:

```
docs/design/
├── system.md        # Source of truth — direction, tokens, component patterns, rationale
├── tokens.css       # Auto-generated CSS custom properties (never edit manually)
└── showcase.html    # Auto-generated component showcase (never edit manually)
```

`system.md` is the source of truth. `tokens.css` and `showcase.html` are always regenerated from it.

### Workflow

```
/stencil cut
  ├── Detect project context (framework, CSS approach)
  ├── Establish aesthetic direction
  ├── Research the style online (fonts, palettes, references)
  ├── [Optional] Brainstorm with Stitch MCP
  ├── Detect needed components from project description
  ├── Generate system.md (tokens + component patterns)
  ├── Generate tokens.css (CSS custom properties)
  ├── Auto-wire token import into project entry point
  ├── Generate showcase.html (all components rendered)
  └── Iterate until user approves

/stencil lay
  └── Regenerate showcase.html from current system.md

/stencil trim
  ├── Interpret change request → token diff
  ├── Update system.md + regenerate tokens.css
  ├── Regenerate showcase → user previews
  ├── On approval → dispatch propagator agents in parallel
  └── Each propagator updates one component file

/stencil check
  ├── Scan all frontend files
  ├── Detect violations (hardcoded colors, off-scale spacing, wrong radius, etc.)
  ├── Report with file:line, current value, should-be value
  └── Offer auto-fix
```

### Token propagation

When you `/stencil trim`, changes propagate in parallel — one agent per component file, batched in groups of 8:

```
Propagation:
├── Agent 1: Button.tsx     (bg-blue-500 → bg-primary)
├── Agent 2: Card.tsx       (shadow-md → shadow-sm)
├── Agent 3: Input.tsx      (rounded-lg → rounded-md)
└── Agent 4: Modal.tsx      (no changes needed)

All 4 run simultaneously.
```

## Attribution

Stencil includes principles adapted from:

- [Emil Kowalski](https://emilkowal.ski/) — Animation decision framework, component building principles
- [Make Interfaces Feel Better](https://github.com/codingcodax/make-interfaces-feel-better) — Surface, typography, and micro-interaction rules
- [frontend-design](https://github.com/anthropics/claude-code) — Copyright Anthropic, Apache 2.0

## License

MIT
