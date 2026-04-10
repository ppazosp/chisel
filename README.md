<p align="center">
  <img src=".github/chisel.png" alt="Chisel" width="200">
</p>

<h1 align="center">Chisel</h1>

<p align="center">
  Design system studio for frontend interfaces.
</p>

<p align="center">
  <a href="https://github.com/ppazosp/chisel/blob/main/LICENSE"><img src="https://img.shields.io/github/license/ppazosp/chisel" alt="License"></a>
  <a href="https://github.com/ppazosp/chisel/releases"><img src="https://img.shields.io/github/v/release/ppazosp/chisel?include_prereleases" alt="Release"></a>
  <a href="https://skills.sh/ppazosp/chisel"><img src="https://img.shields.io/badge/agent_skill-chisel-blue" alt="Agent Skill"></a>
  <a href="https://agentskills.io"><img src="https://img.shields.io/badge/works_with-18%2B_agents-green" alt="Works with 18+ agents"></a>
</p>

<p align="center">
  Cut the design. Carve it into pages. Hone the edges. Inspect the work.<br>
  All state in markdown. Zero external services.
</p>

---

## Install

### Any Agent (recommended)

```bash
npx skills add ppazosp/chisel

# update later
npx skills update
```

This installs Chisel to `~/.agents/skills/chisel/` where it's automatically discovered by Claude Code, Codex, Copilot, Cursor, Gemini CLI, OpenCode, and [18+ other agents](https://agentskills.io).

### Claude Code (plugin mode)

Install as a plugin to get `/chisel:cut`, `/chisel:carve`, `/chisel:hone`, etc. in the autocomplete menu:

```
/plugin marketplace add ppazosp/backpack
/plugin install chisel@backpack
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
    "chisel@backpack": true
  }
}
```

### Manual

Clone into your project and point your agent's instruction file (`AGENTS.md`, `.github/copilot-instructions.md`, etc.) to the `AGENTS.md` file in this repo.

### Recommended: Stitch MCP

[Stitch](https://stitch.withseam.com) generates visual mockups from text descriptions. When connected, `/chisel cut` and `/chisel hone` use it to brainstorm layouts visually before generating tokens — significantly improving design quality.

Install it and add to your MCP config. Chisel will detect it automatically and recommend it if missing.

## Quick Start

```bash
# 1. Cut: establish design direction, generate tokens + showcase
/chisel cut "admin dashboard"

# 2. Carve: build pages using the design system
/chisel carve "dashboard with sidebar and stats"

# 3. Lay: display the component showcase
/chisel lay

# 4. Hone: tweak tokens, preview, propagate
/chisel hone "rounder buttons, warmer palette"

# 5. Inspect: audit code against the design system
/chisel inspect
```

## Commands

| Command | What it does |
|---------|-------------|
| `/chisel cut` | Cut the design — detect context → aesthetic direction → research style → [optional Stitch] → generate tokens + showcase → iterate |
| `/chisel carve` | Carve the pages — build frontend pages using the design system, component by component |
| `/chisel lay` | Lay it out — regenerate the component showcase from current `system.md` |
| `/chisel hone` | Hone the edges — tweak tokens → regenerate showcase → approve → propagate to all components in parallel |
| `/chisel inspect` | Inspect the work — audit all frontend code against `system.md` → report violations → auto-fix |

## Agents

Chisel includes 1 subagent prompt in `agents/`. It's dispatched during `/chisel hone` to propagate token changes.

| Agent | Role | File |
|-------|------|------|
| Propagator | Applies design token changes across component files, one file per agent in parallel | `agents/propagator.md` |

On platforms that support subagents (Claude Code, Codex), multiple propagators are dispatched in parallel — one per component file. On platforms without subagent support, the main agent reads the prompt and follows its instructions inline.

## Always-On Skill: UI Craft

Chisel includes `ui-craft`, a passive skill that's always active when working on frontend code. It enforces micro-polish rules across 5 areas:

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
/chisel cut
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

/chisel carve
  ├── Load design system + project context
  ├── Study purpose, journey, data, layout
  ├── Decompose into components (leaf, composite, page)
  ├── Plan interactions and state flow
  ├── Build in parallel waves (leaf → composite → page)
  ├── Polish pass against ui-craft checklist
  └── Verify and report

/chisel lay
  └── Regenerate showcase.html from current system.md

/chisel hone
  ├── Interpret change request → token diff
  ├── Update system.md + regenerate tokens.css
  ├── Regenerate showcase → user previews
  ├── On approval → dispatch propagator agents in parallel
  └── Each propagator updates one component file

/chisel inspect
  ├── Scan all frontend files
  ├── Detect violations (hardcoded colors, off-scale spacing, wrong radius, etc.)
  ├── Report with file:line, current value, should-be value
  └── Offer auto-fix
```

### Token propagation

When you `/chisel hone`, changes propagate in parallel — one agent per component file, batched in groups of 8:

```
Propagation:
├── Agent 1: Button.tsx     (bg-blue-500 → bg-primary)
├── Agent 2: Card.tsx       (shadow-md → shadow-sm)
├── Agent 3: Input.tsx      (rounded-lg → rounded-md)
└── Agent 4: Modal.tsx      (no changes needed)

All 4 run simultaneously.
```

## Attribution

Chisel includes principles adapted from external sources. See [NOTICE](NOTICE) for full attribution.

## License

MIT
