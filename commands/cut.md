---
description: Start a new design system — direction, tokens, component showcase
argument-hint: Optional description of what you're building
---

# Open

Shape a design system from scratch. Detect context, establish aesthetic direction, generate tokens, build a component showcase, and iterate until it feels right.

**Input:** $ARGUMENTS

---

## Phase 0: Detect State

**Goal:** Check if a design system already exists, load project context

**Actions:**

1. **Check for existing system:**
   - Look for `docs/design/system.md`
   - If it exists: "Design system already exists. Use `/chisel hone` to tweak it or `/chisel lay` to regenerate the showcase."
   - STOP — don't overwrite an existing system without explicit confirmation

2. **Load project context:**
   - Read `README.md`, `package.json`, `CLAUDE.md` if they exist
   - Detect framework: React/Next.js, Vue/Nuxt, Svelte/SvelteKit, Astro, vanilla
   - Detect CSS approach: Tailwind, CSS Modules, styled-components, vanilla CSS
   - Detect if Stitch MCP is available (check for `stitch` in MCP tools)

3. **Parse input:**
   - If `$ARGUMENTS` is empty: ask "What are you building?" (dashboard, app, landing page, tool, etc.)
   - Otherwise: use as project description

---

## Phase 1: Aesthetic Direction

**Goal:** Establish a clear visual direction before generating any tokens

**Process:**

1. **Ask the user:**
   > "What's the vibe? Pick one or describe your own:
   > - **Minimal** — clean, lots of whitespace, subtle
   > - **Bold** — high contrast, dramatic, statement-making
   > - **Playful** — rounded, colorful, friendly
   > - **Industrial** — dense, monochrome, utilitarian
   > - **Editorial** — magazine-like, typography-driven
   > - **Luxury** — refined, elegant, premium feel
   > - Or: **surprise me**"

2. **If "surprise me":** pick a direction that fits the project type. Dashboard → industrial or minimal. Consumer app → playful or bold. Internal tool → minimal or industrial.

3. **Research the vibe online (ALWAYS — even if using Stitch):**
   - Use WebSearch to research the user's chosen aesthetic direction
   - Search for: `"<vibe> UI design"`, `"<vibe> design system examples"`, `"<vibe> web design inspiration"`
   - Look for: color palettes, typography pairings, spacing patterns, radius conventions, shadow styles, real-world examples
   - Extract concrete design decisions from the research (specific fonts, color ranges, radius values, shadow approaches)
   - If the vibe is a specific brand or product ("like Linear", "like Notion", "like Nothing Phone"), research that product's design language specifically
   - Share findings with the user: key references, font pairings, color ranges

4. **Check for Stitch MCP (optional, after research):**
   - Use `ToolSearch` with query `"stitch"` to check if Stitch MCP tools are available
   - If tools like `mcp__stitch__generate_screen_from_text`, `mcp__stitch__create_project`, etc. are found → Stitch IS available
   - If no stitch tools found → skip to step 5

   **If Stitch IS available, offer it:**
   > "Stitch is connected — want me to brainstorm layouts visually using what we found from research?"

   If user says yes:
   - Load the Stitch tool schemas via ToolSearch
   - Create a Stitch project with `mcp__stitch__create_project`
   - Feed the research findings into the prompt — specific fonts, colors, radius, shadow styles discovered
   - Use `mcp__stitch__generate_screen_from_text` to generate 2-3 screen variants based on research + project description + vibe
   - Present them to the user with `mcp__stitch__get_screen`
   - Extract additional visual direction from the chosen variant (layout patterns, component arrangements)
   - Combine research findings + Stitch output to inform token generation in Phase 3

   If user says no → proceed to step 5

5. **Confirm direction with the user before proceeding.** Present the full picture — research findings + Stitch mockup (if used) — so the user knows what's informing the tokens.

---

## Phase 2: Component Detection

**Goal:** Determine which components the showcase needs

**Actions:**

1. **From the project description, infer needed components:**

   | Project type | Likely components |
   |---|---|
   | Dashboard | Card, Table, Chart container, Stat, Nav, Sidebar, Badge, Button, Input, Select, Modal, Dropdown, Avatar, Tabs |
   | Admin panel | Table, Form, Button, Input, Select, Textarea, Checkbox, Radio, Toggle, Modal, Sidebar, Breadcrumb, Badge, Alert |
   | Consumer app | Button, Input, Card, Avatar, Nav, Hero, CTA, Footer, Badge, Toast, Modal |
   | Internal tool | Button, Input, Table, Form, Select, Tabs, Modal, Alert, Badge, Code block |
   | Landing page | Hero, CTA, Feature card, Testimonial, Nav, Footer, Button, Badge |

2. **Present the list:**
   > "Based on what you're building, I think you'll need these components:
   > [list]
   >
   > Add or remove any?"

3. **Get user confirmation.** Add/remove as requested.

---

## Phase 3: Generate Design System

**Goal:** Create `system.md` with tokens, rationale, and component patterns

**Actions:**

1. **Create `docs/design/` directory**

2. **Write `docs/design/system.md`:**

```markdown
# Design System

> Direction: <aesthetic direction>
> Framework: <detected framework>
> CSS: <detected CSS approach>
> Created: YYYY-MM-DD
> Last updated: YYYY-MM-DD

## Direction

<1-2 sentences describing the aesthetic intent and why it fits this project>

## Tokens

### Colors

#### Light Mode
| Token | Value | Usage |
|-------|-------|-------|
| `--color-background` | `#...` | Page background |
| `--color-surface` | `#...` | Card/container background |
| `--color-surface-hover` | `#...` | Hovered surface |
| `--color-border` | `#...` | Borders and dividers |
| `--color-text-primary` | `#...` | Primary text |
| `--color-text-secondary` | `#...` | Secondary/muted text |
| `--color-text-tertiary` | `#...` | Placeholder, disabled |
| `--color-primary` | `#...` | Primary actions, links |
| `--color-primary-hover` | `#...` | Hovered primary |
| `--color-accent` | `#...` | Accent/highlight |
| `--color-destructive` | `#...` | Error, delete, danger |
| `--color-success` | `#...` | Success, confirmation |
| `--color-warning` | `#...` | Warning states |

#### Dark Mode
[Same tokens with dark mode values]

### Typography

| Token | Value | Usage |
|-------|-------|-------|
| `--font-sans` | `'...'` | Body text |
| `--font-display` | `'...'` | Headings (if different) |
| `--font-mono` | `'...'` | Code, data |
| `--text-xs` | `0.75rem` | Labels, captions |
| `--text-sm` | `0.875rem` | Secondary text |
| `--text-base` | `1rem` | Body |
| `--text-lg` | `1.125rem` | Subheadings |
| `--text-xl` | `1.25rem` | Section headers |
| `--text-2xl` | `1.5rem` | Page titles |
| `--text-3xl` | `1.875rem` | Hero/display |
| `--leading-tight` | `1.25` | Headings |
| `--leading-normal` | `1.5` | Body |
| `--weight-normal` | `400` | Body |
| `--weight-medium` | `500` | Emphasis |
| `--weight-semibold` | `600` | Headings |

### Spacing

| Token | Value |
|-------|-------|
| `--space-1` | `4px` |
| `--space-2` | `8px` |
| `--space-3` | `12px` |
| `--space-4` | `16px` |
| `--space-6` | `24px` |
| `--space-8` | `32px` |
| `--space-12` | `48px` |
| `--space-16` | `64px` |

### Shape

| Token | Value | Usage |
|-------|-------|-------|
| `--radius-sm` | `...` | Small elements (badges, chips) |
| `--radius-md` | `...` | Buttons, inputs |
| `--radius-lg` | `...` | Cards, containers |
| `--radius-xl` | `...` | Modals, large surfaces |
| `--radius-full` | `9999px` | Avatars, pills |

### Shadows

| Token | Value | Usage |
|-------|-------|-------|
| `--shadow-sm` | `...` | Subtle depth |
| `--shadow-md` | `...` | Cards, dropdowns |
| `--shadow-lg` | `...` | Modals, popovers |

### Animation

| Token | Value | Usage |
|-------|-------|-------|
| `--duration-fast` | `100ms` | Button feedback |
| `--duration-normal` | `200ms` | Transitions |
| `--duration-slow` | `300ms` | Modals, drawers |
| `--ease-out` | `cubic-bezier(0.23, 1, 0.32, 1)` | Entries |
| `--ease-in-out` | `cubic-bezier(0.77, 0, 0.175, 1)` | Movement |

## Component Patterns

### Button
[Sizes, variants, states, token usage]

### Input
[Sizes, states, token usage]

### Card
[Variants, token usage]

[...one section per detected component]
```

**Adjust all token VALUES to match the aesthetic direction.** Bold direction → higher contrast, stronger shadows. Minimal → subtle shadows, muted palette. Playful → larger radii, saturated colors. Don't use generic defaults — every value should reflect the chosen direction.

---

## Phase 4: Generate tokens.css

**Goal:** Auto-generate CSS custom properties from `system.md`

**Write `docs/design/tokens.css`:**

```css
/* Auto-generated from system.md — do not edit manually */
/* Regenerate with: /chisel lay */

:root {
  /* Colors — Light */
  --color-background: ...;
  --color-surface: ...;
  /* ... all light mode tokens */

  /* Typography */
  --font-sans: ...;
  /* ... all typography tokens */

  /* Spacing */
  --space-1: 4px;
  /* ... all spacing tokens */

  /* Shape */
  --radius-sm: ...;
  /* ... all shape tokens */

  /* Shadows */
  --shadow-sm: ...;
  /* ... all shadow tokens */

  /* Animation */
  --duration-fast: 100ms;
  /* ... all animation tokens */
}

/* Dark mode */
@media (prefers-color-scheme: dark) {
  :root {
    --color-background: ...;
    --color-surface: ...;
    /* ... dark mode color overrides only */
  }
}

/* If project uses class-based dark mode */
.dark {
  --color-background: ...;
  --color-surface: ...;
  /* ... same dark overrides */
}
```

---

## Phase 5: Auto-Wire Token Import

**Goal:** Make the project consume tokens.css without manual setup

**Detect entry point and wire it:**

| Framework | Entry point | How to wire |
|---|---|---|
| Next.js | `app/layout.tsx` or `pages/_app.tsx` | Add `import '../docs/design/tokens.css'` |
| React (Vite) | `src/main.tsx` | Add `import '../docs/design/tokens.css'` |
| Vue/Nuxt | `nuxt.config.ts` or `src/main.ts` | Add to CSS array or import |
| Svelte/SvelteKit | `src/app.html` or `+layout.svelte` | Add `<link>` or import |
| Astro | `src/layouts/Layout.astro` | Add `<link>` or import |
| Vanilla | `index.html` | Add `<link rel="stylesheet" href="docs/design/tokens.css">` |

**If Tailwind is detected**, also generate a snippet for `tailwind.config.js`/`tailwind.config.ts` that maps CSS vars to Tailwind utilities:

```js
// Add to tailwind.config.js theme.extend
colors: {
  background: 'var(--color-background)',
  surface: 'var(--color-surface)',
  'surface-hover': 'var(--color-surface-hover)',
  border: 'var(--color-border)',
  'text-primary': 'var(--color-text-primary)',
  'text-secondary': 'var(--color-text-secondary)',
  primary: 'var(--color-primary)',
  'primary-hover': 'var(--color-primary-hover)',
  accent: 'var(--color-accent)',
  destructive: 'var(--color-destructive)',
  success: 'var(--color-success)',
  warning: 'var(--color-warning)',
},
borderRadius: {
  sm: 'var(--radius-sm)',
  md: 'var(--radius-md)',
  lg: 'var(--radius-lg)',
  xl: 'var(--radius-xl)',
},
boxShadow: {
  sm: 'var(--shadow-sm)',
  md: 'var(--shadow-md)',
  lg: 'var(--shadow-lg)',
},
```

**Apply the Tailwind config change automatically.** Read the existing config, merge the theme extension, write it back.

---

## Phase 6: Generate Showcase

**Goal:** Build a standalone HTML file rendering every component with the design tokens

**Write `docs/design/showcase.html`:**

A single self-contained HTML file that:

1. Imports `tokens.css` via `<link>`
2. Renders every detected component in its own labeled section
3. Shows all variants and states (default, hover, active, disabled, focus)
4. Includes a light/dark mode toggle
5. Has a clean layout with a sidebar nav listing all components

**Each component section shows:**
- Component name and description
- All size variants side by side
- All color/style variants
- Interactive states (hover, active, disabled) — use CSS `:hover` etc.
- Code snippet showing token usage

**The showcase must be self-contained — no build step, no framework dependency.** Pure HTML + CSS + minimal JS (for dark mode toggle and nav). It uses the same CSS tokens the real components will use, so what you see in the showcase IS what the components will look like.

---

## Phase 7: Open and Iterate

**Goal:** User reviews the showcase and iterates until satisfied

**Actions:**

1. **Open the showcase:**
   ```bash
   open docs/design/showcase.html
   ```

2. **Ask:**
   > "Showcase is open. What do you want to change? Examples:
   > - 'buttons too rounded'
   > - 'darker background'
   > - 'more contrast between text levels'
   > - 'swap to a blue primary'
   > - 'looks good, let's go'"

3. **For each change:**
   - Update the relevant tokens in `system.md`
   - Regenerate `tokens.css`
   - Regenerate `showcase.html`
   - Re-open showcase
   - Ask again

4. **When user approves ("looks good", "let's go", "ship it"):**
   - Commit all design files: `feat[design]: initialize design system`
   - Present summary of tokens and components
   - Remind: "Use `/chisel hone` to tweak later, `/chisel inspect` to audit code against the system."

---

## Autonomous Decision Guidelines

| Situation | Default Decision |
|-----------|------------------|
| Font choice | Match direction: minimal→Inter/Geist, bold→Syne/Space Grotesk, playful→Nunito/Quicksand, industrial→JetBrains Mono/IBM Plex, editorial→Libre Baskerville/Playfair, luxury→Cormorant/Tenor Sans |
| Color palette | Derive from direction, always with sufficient contrast ratios (WCAG AA) |
| Border radius | Minimal→4-8px, playful→12-16px, industrial→2-4px, bold→0-4px or 16px+ |
| Shadow style | Minimal→barely visible, bold→strong drop shadows, industrial→none, luxury→soft diffused |
| Spacing scale | Always 4px base, adjust density for project type (dashboard→tighter, marketing→looser) |
| Dark mode | Always generate both modes |
| Components | Include all detected + Button, Input (always needed) |

---

## Red Flags — STOP and ask user if:

- Project already has a design system / component library
- No clear entry point for token import (unusual project structure)
- User's aesthetic direction is contradictory ("minimal but also bold and colorful")
- Stitch MCP is requested but not available
