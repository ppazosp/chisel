# Chisel — Full Reference

This document is the compiled reference for Chisel, a design system studio for frontend interfaces. It contains all commands, the agent prompt, the always-on skill, and all reference material in a single document.

Agents and LLMs should follow the instructions in this document. Humans may also find it useful, but guidance here is optimized for automation.

---

# Design System Files

All design state lives in `docs/design/` at the project root:

```
docs/design/
├── system.md        # Source of truth — direction, tokens, component patterns, rationale
├── tokens.css       # Auto-generated CSS custom properties (never edit manually)
└── showcase.html    # Auto-generated component showcase (never edit manually)
```

`system.md` is the source of truth. `tokens.css` and `showcase.html` are always regenerated from it.

---

# Command: /cut

Start a new design system from scratch. Detect context, establish aesthetic direction, generate tokens, build a component showcase, iterate until it feels right.

**Input:** `$ARGUMENTS` — optional description of what you're building

## Phase 0: Detect State

1. Check for existing `docs/design/system.md` — if it exists, stop and suggest `/hone` or `/lay`
2. Load project context: `README.md`, `package.json`, `CLAUDE.md`
3. Detect framework (React/Next.js, Vue/Nuxt, Svelte/SvelteKit, Astro, vanilla) and CSS approach (Tailwind, CSS Modules, styled-components, vanilla CSS)
4. Check if Stitch MCP is available
5. Parse input: if `$ARGUMENTS` is empty, ask "What are you building?". Otherwise use as project description.

## Phase 1: Aesthetic Direction

1. **Try to infer the vibe from the project description first:**

   | Signals | Auto-select |
   |---|---|
   | Finance, crypto, trading, analytics, monitoring | Industrial |
   | Dashboard, admin panel, internal tool, developer | Minimal |
   | Kids, game, social, fun, colorful | Playful |
   | Fashion, jewelry, real estate, premium | Luxury |
   | Blog, magazine, news, content, publishing | Editorial |
   | Startup, SaaS, landing page, marketing | Bold |

   If clear match: auto-select, tell the user the choice and why, offer to change. Skip to research.
   If ambiguous: ask the user (Minimal, Bold, Playful, Industrial, Editorial, Luxury, or "surprise me").
2. If "surprise me": pick based on the table above
3. **Research the vibe online (ALWAYS)** — search for `"<vibe> UI design"`, `"<vibe> design system examples"`, extract concrete design decisions (fonts, color ranges, radius, shadows)
4. Check for Stitch MCP. If available: offer visual brainstorming (significantly improves design quality). If not available: recommend installing it at https://stitch.withseam.com — don't block, proceed.
5. Confirm direction with user before proceeding

## Phase 2: Component Detection

Infer components from project type:

| Project type | Likely components |
|---|---|
| Dashboard | Card, Table, Chart container, Stat, Nav, Sidebar, Badge, Button, Input, Select, Modal, Dropdown, Avatar, Tabs |
| Admin panel | Table, Form, Button, Input, Select, Textarea, Checkbox, Radio, Toggle, Modal, Sidebar, Breadcrumb, Badge, Alert |
| Consumer app | Button, Input, Card, Avatar, Nav, Hero, CTA, Footer, Badge, Toast, Modal |
| Internal tool | Button, Input, Table, Form, Select, Tabs, Modal, Alert, Badge, Code block |
| Landing page | Hero, CTA, Feature card, Testimonial, Nav, Footer, Button, Badge |

Present list, get user confirmation.

## Phase 3: Generate Design System

Write `docs/design/system.md` with:

- Direction header (aesthetic direction, framework, CSS approach, dates)
- Direction description (1-2 sentences)
- Token tables: Colors (light + dark), Typography, Spacing, Shape, Shadows, Animation
- Component patterns section (one subsection per detected component)

**All token values must reflect the chosen direction.** Bold → high contrast, strong shadows. Minimal → subtle shadows, muted. Playful → large radii, saturated colors. No generic defaults.

## Phase 4: Generate tokens.css

Auto-generate CSS custom properties from `system.md`. Light mode in `:root`, dark mode in `@media (prefers-color-scheme: dark)` and `.dark` class.

## Phase 5: Auto-Wire Token Import

Detect entry point by framework and wire the import:

| Framework | Entry point | How to wire |
|---|---|---|
| Next.js | `app/layout.tsx` or `pages/_app.tsx` | `import '../docs/design/tokens.css'` |
| React (Vite) | `src/main.tsx` | `import '../docs/design/tokens.css'` |
| Vue/Nuxt | `nuxt.config.ts` or `src/main.ts` | CSS array or import |
| Svelte/SvelteKit | `src/app.html` or `+layout.svelte` | `<link>` or import |
| Astro | `src/layouts/Layout.astro` | `<link>` or import |
| Vanilla | `index.html` | `<link rel="stylesheet" href="docs/design/tokens.css">` |

If Tailwind detected: generate `tailwind.config.js` extension mapping CSS vars to Tailwind utilities.

## Phase 6: Generate Showcase

Write `docs/design/showcase.html` — self-contained HTML with linked `tokens.css`, every component in labeled sections with variants/states, light/dark toggle, sidebar nav. No build step required.

## Phase 7: Open and Iterate

Open showcase, ask for feedback. For each change: update tokens in `system.md`, regenerate `tokens.css` and `showcase.html`, re-open. When approved: commit `feat[design]: initialize design system`.

## Autonomous Decision Guidelines

| Situation | Default Decision |
|-----------|------------------|
| Font choice | Match direction: minimal→Inter/Geist, bold→Syne/Space Grotesk, playful→Nunito/Quicksand, industrial→JetBrains Mono/IBM Plex, editorial→Libre Baskerville/Playfair, luxury→Cormorant/Tenor Sans |
| Color palette | Derive from direction, WCAG AA contrast |
| Border radius | Minimal→4-8px, playful→12-16px, industrial→2-4px, bold→0-4px or 16px+ |
| Shadow style | Minimal→barely visible, bold→strong drop shadows, industrial→none, luxury→soft diffused |
| Spacing scale | 4px base, adjust density for project type (dashboard→tighter, marketing→looser) |
| Dark mode | Always generate both modes |
| Components | Always include Button, Input |

---

# Command: /carve

Build frontend pages using the design system. Think deeply about purpose, decompose into components, plan layout and interactions, then dispatch parallel agents to build each component simultaneously.

**Input:** `$ARGUMENTS` — what to build (e.g. "dashboard with sidebar", "settings page", "login form")

## Phase 0: Pick Up the Chisel

1. Check for `docs/design/system.md` — if missing, stop and suggest `/cut`
2. Read `system.md`, `tokens.css`, `showcase.html` fully
3. Read `CLAUDE.md` if it exists
4. Detect framework and CSS approach
5. Scan existing components (glob `src/components/**/*.{tsx,jsx,vue,svelte}`)
6. If `$ARGUMENTS` is empty: ask "What page do you want to build?"

## Phase 1: Study the Stone — Purpose

**No code. Think first.**

1. What is this page for? What problem does it solve?
2. Who uses it? What role, context, mood?
3. What's the primary action?
4. What's the user's mental model?

Present and confirm before proceeding.

## Phase 2: Study the Stone — Journey and Features

1. Walk the user journey step by step
2. List every feature as interaction + outcome
3. List page states: empty, loading, populated, error

## Phase 3: Study the Stone — Information and Data

1. Information hierarchy: primary → secondary → tertiary → actions
2. TypeScript types for every data entity
3. Realistic placeholder data (diverse names, varied numbers, recent dates)

## Phase 4: Mark the Lines — Layout

1. Text diagram of the layout
2. CSS Grid or Flexbox per level with gap/alignment
3. Responsive behavior and breakpoints

## Phase 5: Mark the Lines — Components

1. Walk layout diagram naming every piece
2. Classify each: Exists, Exists in system, New leaf, New composite, Page layout
3. For each new component: props, variants, states, tokens used

## Phase 6: Mark the Lines — Interactions and State

1. Interaction map (what triggers what)
2. State ownership (page-level vs component-level)
3. State flow diagram (parent → children, props/events)

## Phase 7: Step Back — Confirm the Plan

Present: purpose, layout diagram, components list, state flow, build plan by wave. **Get user confirmation.** Last checkpoint before parallel build.

## Phase 8: Strike — Build in Parallel

**Three waves:**

**Wave 1: Leaf components (parallel).** Dispatch one agent per leaf component. Each receives: component spec, full `system.md`, framework context, ui-craft rules, file path. Max 8 parallel. Wait for completion.

**Wave 2: Composite components (parallel).** Each agent receives Wave 1 output paths + layout spec + interaction map. Wait for completion.

**Wave 3: Page assembly (single agent).** Imports all components, defines page-level state, wires interactions, implements responsive layout, adds placeholder data.

**All agents must follow ui-craft rules:** concentric radius, shadows over borders, optical alignment, `tabular-nums`, `text-wrap: balance`, `scale(0.97)` on press, specific `transition-property`, 44x44px hit areas, `ease-out` entries, `prefers-reduced-motion`, hover gated behind media query.

## Phase 9: Sand — Polish Pass

Review every file against the 17-point ui-craft checklist. Fix violations.

## Phase 10: Inspect — Verify and Report

Run lint/typecheck, report what was built by wave with file paths, suggest next steps.

## Autonomous Decision Guidelines

| Situation | Default Decision |
|-----------|------------------|
| Layout approach | CSS Grid for 2D, Flexbox for 1D |
| Responsive | Mobile-first with `sm:`, `md:`, `lg:` |
| Placeholder data | Realistic, diverse, varied |
| State management | Local state only — no global stores, no API calls |
| Animations | ui-craft: ease-out entries, stagger 30-80ms, under 300ms |
| Missing tokens | Add to `system.md` and `tokens.css` before using |
| Component needs >5 props | Consider splitting |

---

# Command: /lay

Regenerate the component showcase HTML from current `system.md` tokens and component patterns.

## Phase 0: Load Design System

1. Check for `docs/design/system.md` — if missing, stop and suggest `/cut`
2. Parse all token values and component patterns
3. Read `tokens.css` — if missing or out of sync, regenerate first

## Phase 1: Detect Components

1. Extract components from `system.md` component patterns section
2. Scan codebase for component files added since last showcase
3. Cross-reference and note new components

## Phase 2: Generate Showcase HTML

Write `docs/design/showcase.html` — self-contained HTML:

- `<link rel="stylesheet" href="tokens.css">`
- Embedded styles for showcase layout, minimal JS for dark mode toggle and nav
- Sidebar nav listing every component
- Per-component sections: header, variants grid, size scale, interactive CSS states, dark mode toggle, token annotations
- Footer: "Generated by Chisel · Last updated: YYYY-MM-DD"

No build step. No framework dependency.

## Phase 3: Open and Report

Open `showcase.html`, report component count, flag any codebase components not in `system.md`.

---

# Command: /hone

Change design tokens, preview in the showcase, and propagate approved changes to all component files.

**Input:** `$ARGUMENTS` — what to change (e.g. "warmer palette", "rounder buttons", "smaller text")

## Phase 0: Load Design System

1. Check for `docs/design/system.md` — if missing, stop and suggest `/cut`
2. Read `system.md` fully
3. If `$ARGUMENTS` is empty: ask "What do you want to change?"

## Phase 1: Interpret Change

1. Classify: **Concrete** (direct token mapping) or **Stylistic** (needs research)
2. If stylistic: **research online (ALWAYS)** — search for the style, extract concrete token values. Optionally offer Stitch MCP mockups.
3. Map request to token changes
4. Present diff (old → new values)
5. Get confirmation

## Phase 2: Update System Files

1. Update token values in `system.md`, update `Last updated` date
2. Regenerate `tokens.css`
3. Update Tailwind config if new tokens were added

## Phase 3: Regenerate Showcase

Regenerate `showcase.html`, open it. Ask: "looks good" (propagate), "tweak more" (back to Phase 1), or "revert" (restore from git).

## Phase 4: Propagate to Components

1. Glob for component files (`src/components/**/*.{tsx,jsx,vue,svelte,html,css}`, `src/app/**/*`, `src/pages/**/*`)
2. Prepare shared context: full `system.md`, token changes with old→new, framework context
3. Dispatch one **propagator** agent per component file, in parallel (max 8 per batch)
4. Collect results, run lint/typecheck, report changes
5. Commit: `feat[design]: refine design tokens — <brief description>`

## Autonomous Decision Guidelines

| Situation | Default Decision |
|-----------|------------------|
| Ambiguous change | Ask for clarification |
| Contrast affected | Verify WCAG AA compliance |
| Primary color changed | Also update primary-hover (darken 10%) |
| Background changed | Check surface, border consistency |
| Radius changed | Maintain concentric relationships |

---

# Command: /inspect

Audit all frontend code against the design system. Report violations, offer auto-fix.

## Phase 0: Load Design System

1. Check for `docs/design/system.md` — if missing, stop and suggest `/cut`
2. Read `system.md` and `tokens.css` fully

## Phase 1: Scan Frontend Files

Glob for: `src/**/*.{tsx,jsx,vue,svelte,html,css,scss,less}`, `app/**/*`, `pages/**/*`, `components/**/*`. Exclude: `node_modules/`, `docs/design/`, build output, test files.

## Phase 2: Detect Violations

Check each file for:

- **Color:** hardcoded hex/rgb/hsl, Tailwind color classes that should use token classes
- **Spacing:** off-scale px values, arbitrary Tailwind values when tokens exist
- **Typography:** wrong font family/size/weight, missing `tabular-nums`
- **Shape:** off-token radius, non-concentric nested radius
- **Shadow:** hardcoded `box-shadow`, borders used for depth where shadows should be
- **Animation:** `transition: all`, off-scale durations, wrong easings, missing `prefers-reduced-motion`
- **Component patterns:** deviations from `system.md` component patterns

## Phase 3: Report

Markdown table with:
- **Critical** — hardcoded values that must use tokens (file, line, current, should-be)
- **Warnings** — inconsistencies (file, line, issue, details)
- **Clean** — files with no violations

## Phase 4: Auto-Fix

Ask "fix all" or "review one by one". Apply fixes per file, run lint, regenerate showcase, report, commit: `fix[design]: resolve design system violations`.

## Autonomous Decision Guidelines

| Situation | Default Decision |
|-----------|------------------|
| Near-match to token | Suggest nearest token |
| No matching token | Flag as warning, not critical |
| File uses no tokens | Might not be a UI file — flag but don't auto-fix |
| Third-party component | Skip |
| CSS-in-JS dynamic values | Flag hardcoded parts, skip computed |

---

# Agent: Propagator

Applies design token changes to a single component file. Replaces hardcoded values with token references and updates changed values.

**Input:** file path, `system.md` content, token changes (old/new values), framework context.

**Process:**

1. Read the component file fully
2. Identify all design values (colors, spacing, radius, shadows, fonts, transitions, opacity)
3. Apply decision table:

   | Current value | Token exists? | Action |
   |---|---|---|
   | Hardcoded, exact match | Yes | Replace with token reference |
   | Hardcoded, close (~2px/~5%) | Yes | Replace with nearest, note in output |
   | Hardcoded, no match | No | Leave as-is |
   | Already a token reference | N/A | Leave as-is |
   | Tailwind class, mapping exists | Yes | Replace with mapped class |

4. Apply replacements via Edit tool
5. Report per-line changes

**CSS variable syntax by context:** vanilla CSS → `var(--token)`, Tailwind custom theme → `bg-primary`, Tailwind arbitrary → `bg-[var(--color-primary)]`, CSS-in-JS → `'var(--color-primary)'`.

**Rules:** One file only. Preserve functionality. When in doubt, leave it. Respect intentional overrides. Maintain consistency within the file (don't mix Tailwind and CSS vars).

---

# Always-On Skill: UI Craft

Small details compound into great interfaces. Apply these principles when building or reviewing any frontend code.

## Core Principles

### 1. Concentric Border Radius

`outerRadius = innerRadius + padding`. Mismatched radii on nested elements is the most common thing that makes interfaces feel off. If padding > 24px, treat as separate surfaces.

```css
.card { border-radius: 20px; padding: 8px; }      /* 12 + 8 = 20 */
.card-inner { border-radius: 12px; }
```

### 2. Optical Over Geometric Alignment

When geometric centering looks off, align optically. Buttons with icons: icon-side padding = text-side padding - 2px. Play triangles: shift right 2px. Asymmetric icons: fix SVG directly or `margin-left: 1px`.

### 3. Shadows Over Borders

Layer multiple transparent `box-shadow` values for natural depth. Shadows adapt to any background; solid borders don't. Keep borders for dividers and layout separation.

```css
:root {
  --shadow-border:
    0px 0px 0px 1px rgba(0, 0, 0, 0.06),
    0px 1px 2px -1px rgba(0, 0, 0, 0.06),
    0px 2px 4px 0px rgba(0, 0, 0, 0.04);
}
```

### 4. Animate with Purpose

Before writing animation code, answer: (1) should this animate at all? (2) what is the purpose? (3) what easing? (4) how fast? If the only answer is "it looks cool" and users see it often, don't animate. **Never animate keyboard-initiated actions.**

### 5. Interruptible Over Predetermined

CSS transitions for interactive state changes (hover, toggle, open/close) — they retarget mid-animation. Keyframes for one-shot sequences. Springs for gestures and drag.

### 6. Split and Stagger Enters

Don't animate a single container. Break into semantic chunks, stagger 30-80ms delay between items. Combine `opacity`, `blur`, `translateY`.

```tsx
<motion.div
  initial="hidden" animate="visible"
  variants={{ visible: { transition: { staggerChildren: 0.05 } } }}
>
  <motion.h1 variants={{
    hidden: { opacity: 0, y: 12, filter: "blur(4px)" },
    visible: { opacity: 1, y: 0, filter: "blur(0px)" },
  }} />
</motion.div>
```

### 7. Subtle Exits

Small fixed `translateY` (e.g., -12px), not full height. Exit duration shorter than enter (150ms vs 300ms). The user's focus is moving on.

### 8. Never Animate from scale(0)

Start from `scale(0.95)` or higher with `opacity: 0`.

### 9. Scale on Press

`scale(0.97)` on `:active`. Never below `0.95`. Use CSS transitions for interruptibility.

```css
.button { transition: transform 160ms ease-out; }
.button:active { transform: scale(0.97); }
```

### 10. Ease-out for Entries

`ease-out` starts fast — feels responsive. `ease-in` starts slow — feels sluggish. Use custom curves:

```css
--ease-out: cubic-bezier(0.23, 1, 0.32, 1);
--ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);
```

### 11. UI Animations Under 300ms

| Element | Duration |
|---|---|
| Button press | 100-160ms |
| Tooltips, small popovers | 125-200ms |
| Dropdowns, selects | 150-250ms |
| Modals, drawers | 200-500ms |

### 12. Font Smoothing

`-webkit-font-smoothing: antialiased` at the root on macOS.

### 13. Tabular Numbers

`font-variant-numeric: tabular-nums` for any dynamically updating numbers.

### 14. Text Wrapping

`text-wrap: balance` on headings (max 6 lines). `text-wrap: pretty` for body text.

### 15. Image Outlines

`1px` outline with low opacity, `outline-offset: -1px`.

### 16. Never Use `transition: all`

Always specify exact properties. Tailwind's `transition-transform` covers `transform, translate, scale, rotate`.

### 17. Use `will-change` Sparingly

Only for `transform`, `opacity`, `filter`, `clip-path`. Never `all`. Only when first-frame stutter observed.

### 18. Minimum Hit Area

44x44px (WCAG). Extend with pseudo-element if visible element is smaller.

### 19. Origin-Aware Popovers

Popovers scale from trigger (`transform-origin: var(--radix-popover-content-transform-origin)`). Modals stay `transform-origin: center`.

### 20. Tooltips Skip Delay on Subsequent Hovers

First tooltip delays. Once one is open, adjacent tooltips open instantly with no animation.

### 21. Accessibility

Gate hover animations behind `@media (hover: hover) and (pointer: fine)`. Respect `prefers-reduced-motion`.

## Animation Reference

### Decision Framework

| Frequency | Decision |
|---|---|
| 100+ times/day (keyboard shortcuts) | No animation |
| Tens/day (hover effects) | Remove or reduce |
| Occasional (modals, toasts) | Standard animation |
| Rare/first-time (onboarding) | Can add delight |

### Easing Selection

```
Entering → ease-out
Moving/morphing → ease-in-out
Hover/color → ease
Constant motion → linear
Default → ease-out
```

### CSS Transitions vs Keyframes

Transitions: interruptible, retarget mid-animation → interactive state changes. Keyframes: fixed, restart from beginning → one-shot sequences.

### Contextual Icon Animations

`scale: 0.25→1`, `opacity: 0→1`, `filter: blur(4px)→blur(0px)`, spring with `bounce: 0`.

### Spring Animations

Use for: drag interactions with momentum, "alive" elements, interruptible gestures, decorative mouse tracking. Config: `{ type: "spring", duration: 0.5, bounce: 0.2 }`. Keep bounce subtle (0.1-0.3).

### clip-path Animation

`clip-path: inset(top right bottom left)` — for tab transitions, hold-to-delete, image reveals, comparison sliders.

### @starting-style

CSS-only enter animations without `useEffect` + `setMounted(true)`:

```css
.toast {
  opacity: 1; transform: translateY(0);
  transition: opacity 400ms ease, transform 400ms ease;
  @starting-style { opacity: 0; transform: translateY(100%); }
}
```

## Surfaces Reference

### Shadow as Border

```css
:root {
  --shadow-border:
    0px 0px 0px 1px rgba(0, 0, 0, 0.06),
    0px 1px 2px -1px rgba(0, 0, 0, 0.06),
    0px 2px 4px 0px rgba(0, 0, 0, 0.04);
}
/* Dark: 0 0 0 1px rgba(255, 255, 255, 0.08) */
```

| Use shadows | Use borders |
|---|---|
| Cards, containers with depth | Dividers between list items |
| Buttons with bordered styles | Table cell boundaries |
| Elevated elements | Form input outlines (a11y) |

### Minimum Hit Area Pattern

```css
.checkbox { position: relative; width: 20px; height: 20px; }
.checkbox::after {
  content: ""; position: absolute;
  top: 50%; left: 50%; transform: translate(-50%, -50%);
  width: 44px; height: 44px;
}
```

## Typography Reference

### text-wrap: balance

Headings ≤6 lines (Chromium) / ≤10 (Firefox). Tailwind: `text-balance`.

### text-wrap: pretty

Body copy. Avoids orphans on last line.

### Font Smoothing

```css
html { -webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale; }
```

### Tabular Numbers

| Use | Don't use |
|---|---|
| Counters, timers, prices | Static display numbers |
| Table columns, scoreboards | Phone numbers, version numbers |

## Gestures Reference

### Momentum-Based Dismissal

Calculate velocity — a quick flick dismisses regardless of distance:

```js
const velocity = Math.abs(swipeAmount) / timeTaken;
if (Math.abs(swipeAmount) >= SWIPE_THRESHOLD || velocity > 0.11) dismiss();
```

### Damping at Boundaries

Apply increasing friction past natural boundary. Things slow down before stopping.

### Pointer Capture

`element.setPointerCapture(event.pointerId)` — ensures drag continues when pointer leaves element bounds.

### Friction Instead of Hard Stops

```js
const dampedPosition = boundary + overshoot * (1 / (1 + overshoot * 0.01));
```

### Spring-Based Drag Release

Springs maintain velocity when interrupted. Use `useSpring` with velocity check on `onDragEnd`.

## Performance Reference

### GPU-Compositable Properties

`transform`, `opacity`, `filter`, `clip-path` — skip layout and paint.

### Never `transition: all`

```css
/* Good */ transition-property: scale, background-color;
/* Bad */  transition: all 150ms;
```

### CSS Variables Are Inheritable

Updating a var on parent recalculates all children. For gesture state, use `element.style.transform` directly.

### Framer Motion GPU Caveat

Shorthand `x`/`y`/`scale` are NOT hardware-accelerated. Use `transform: "translateX(100px)"` string form.

### CSS Animations Beat JS Under Load

CSS runs off main thread. Under load, RRAF drops frames. Use CSS for predetermined, JS for dynamic/interruptible.

### Web Animations API (WAAPI)

JS control with CSS performance. Hardware-accelerated, interruptible, no library:

```js
element.animate(
  [{ clipPath: 'inset(0 0 100% 0)' }, { clipPath: 'inset(0 0 0 0)' }],
  { duration: 1000, fill: 'forwards', easing: 'cubic-bezier(0.77, 0, 0.175, 1)' }
);
```

## Review Checklist

- [ ] Nested rounded elements use concentric border radius
- [ ] Icons are optically centered
- [ ] Shadows used instead of borders for depth
- [ ] Every animation has a clear purpose
- [ ] Enter animations split and staggered (30-80ms)
- [ ] Exit animations subtle and shorter than enters
- [ ] No `scale(0)` — minimum `scale(0.95)` with opacity
- [ ] Buttons use `scale(0.97)` on press
- [ ] Easing is `ease-out` for entries, custom curves
- [ ] UI animation durations under 300ms
- [ ] Dynamic numbers use `tabular-nums`
- [ ] Font smoothing applied at root
- [ ] Headings use `text-wrap: balance`
- [ ] Images have subtle outlines
- [ ] No `transition: all`
- [ ] `will-change` only on transform/opacity/filter
- [ ] Hit areas at least 44x44px
- [ ] Popovers scale from trigger, modals from center
- [ ] Hover gated behind `@media (hover: hover) and (pointer: fine)`
- [ ] `prefers-reduced-motion` respected
- [ ] No animation on keyboard-initiated actions

## Common Mistakes

| Mistake | Fix |
|---|---|
| Same border radius on parent and child | `outerRadius = innerRadius + padding` |
| Icons look off-center | Adjust optically or fix SVG |
| Hard borders between sections | Use layered `box-shadow` |
| Jarring enter/exit | Split, stagger, exits softer |
| Numbers cause layout shift | `tabular-nums` |
| `transition: all` | Specify exact properties |
| `scale(0)` entry | `scale(0.95)` with `opacity: 0` |
| `ease-in` on UI | `ease-out` or custom curve |
| Duration > 300ms | Reduce to 150-250ms |
| `transform-origin: center` on popover | Set to trigger location |
| Hover without media query | `@media (hover: hover) and (pointer: fine)` |
| Framer Motion `x`/`y` under load | `transform: "translateX()"` |
