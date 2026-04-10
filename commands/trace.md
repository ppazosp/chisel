---
description: Trace the stencil — build frontend pages using the design system
argument-hint: What to build (e.g. "dashboard with sidebar and stats", "settings page", "login form")
---

# Trace

Build frontend pages by tracing the stencil. Every component follows `system.md`, every value comes from `tokens.css`. Pure frontend — no backend, no API integration.

**Input:** $ARGUMENTS

---

## Phase 0: Load Design System

**Goal:** Read the stencil before tracing

**Actions:**

1. **Check for `docs/design/system.md`:**
   - If missing: STOP — "No design system found. Run `/chisel cut` first."

2. **Read fully:**
   - `docs/design/system.md` — all tokens, direction, component patterns
   - `docs/design/tokens.css` — the CSS custom properties
   - `docs/design/showcase.html` — reference for how components look

3. **Read `CLAUDE.md`** if it exists — project conventions, stack, patterns

4. **Detect framework:**
   - React/Next.js, Vue/Nuxt, Svelte/SvelteKit, Astro, vanilla
   - Tailwind yes/no
   - Component directory convention (e.g., `src/components/`, `components/`)

5. **If `$ARGUMENTS` is empty:** ask "What page do you want to build?"

---

## Phase 1: Plan the Page

**Goal:** Break the page into components before writing code

**Process:**

1. **Parse the request.** Understand what the page needs:
   - Layout structure (sidebar? header? grid? single column?)
   - Which components from `system.md` are needed
   - Which components are NEW and need to be created

2. **Present the component plan:**
   > "To build this page I need:
   >
   > **Existing components (from design system):**
   > - Button — already defined in system.md
   > - Card — already defined in system.md
   >
   > **New components to create:**
   > - StatsRow — grid of stat cards
   > - ActivityTable — data table with rows
   >
   > **Page layout:**
   > - Sidebar (left) + main content (right)
   > - Stats row at top
   > - Activity table below
   >
   > Good to go?"

3. **Get user confirmation.** Adjust if needed.

---

## Phase 2: Build Components

**Goal:** Create each component file, one at a time, using design tokens

**CRITICAL: Invoke the `ui-craft` skill mentally for every component.** Apply its principles:
- Concentric border radius on nested elements
- Shadows over borders for depth
- Optical alignment on icons
- `tabular-nums` on dynamic numbers
- `text-wrap: balance` on headings
- Scale on press for interactive elements
- Stagger animations on lists
- `transition-property` specific, never `all`
- Hit areas at least 44x44px
- `ease-out` for entries, custom curves
- `prefers-reduced-motion` respected

**For each component:**

1. **Check if it already exists** in the codebase — don't recreate existing components
2. **Create one file per component** — clean separation, no multi-component files
3. **All design values MUST come from tokens:**
   - Colors → `var(--color-*)` or Tailwind mapped classes
   - Spacing → `var(--space-*)` or Tailwind mapped classes
   - Radius → `var(--radius-*)` or Tailwind mapped classes
   - Shadows → `var(--shadow-*)` or Tailwind mapped classes
   - Typography → `var(--font-*)`, `var(--text-*)` or Tailwind mapped classes
   - Animation → `var(--duration-*)`, `var(--ease-*)` or Tailwind mapped classes
4. **Follow the component patterns** defined in `system.md` — sizes, variants, states
5. **Include all interactive states:** default, hover, active, focus-visible, disabled
6. **Gate hover behind** `@media (hover: hover) and (pointer: fine)`

**Order:** Build leaf components first (Button, Badge, Input), then composite components (StatsRow, ActivityTable), then the page.

---

## Phase 3: Build the Page

**Goal:** Compose the page from the components

**Actions:**

1. **Create the page file** in the framework's expected location:
   - Next.js → `app/<route>/page.tsx` or `pages/<route>.tsx`
   - Vue/Nuxt → `pages/<route>.vue`
   - SvelteKit → `src/routes/<route>/+page.svelte`
   - Astro → `src/pages/<route>.astro`
   - Vanilla → `<route>/index.html`

2. **Import all components** used in the page

3. **Build the layout:**
   - Use CSS Grid or Flexbox for page structure
   - All spacing from token scale
   - Responsive by default — mobile-first, breakpoints where needed

4. **Use placeholder data** — realistic but clearly fake:
   - Names: "Akira Tanaka", "Sofia Reyes" (not "John Doe")
   - Numbers: varied, not round (1,247 not 1,000)
   - Dates: recent, relative to today
   - Images: use placeholder services or solid color blocks with initials

5. **Wire up basic interactivity:**
   - Navigation highlighting
   - Button click handlers (console.log or state toggle)
   - Input state management
   - No API calls — just frontend state

---

## Phase 4: Polish Pass

**Goal:** Apply ui-craft principles to everything just built

**Review every component and page file against the ui-craft checklist:**

- [ ] Nested rounded elements use concentric border radius
- [ ] Icons are optically centered
- [ ] Shadows used instead of borders for depth
- [ ] Enter animations split and staggered (30-80ms)
- [ ] Dynamic numbers use `tabular-nums`
- [ ] Font smoothing applied at root
- [ ] Headings use `text-wrap: balance`
- [ ] Images have subtle outlines
- [ ] No `transition: all`
- [ ] Buttons use `scale(0.97)` on press
- [ ] Hit areas at least 44x44px
- [ ] Popovers scale from trigger
- [ ] Hover gated behind media query
- [ ] `prefers-reduced-motion` respected
- [ ] No hardcoded design values — all from tokens

**Fix any violations before proceeding.**

---

## Phase 5: Verify and Report

**Goal:** Make sure it runs and looks right

**Actions:**

1. **Run lint/typecheck** if available — fix any errors
2. **Report what was built:**
   ```
   Built: Dashboard page

   Components created:
     src/components/StatsCard.tsx — stat display with icon, value, label
     src/components/StatsRow.tsx — responsive grid of StatsCards
     src/components/ActivityTable.tsx — data table with sortable headers
     src/components/ActivityRow.tsx — single table row with avatar, action, timestamp

   Components reused (already existed):
     src/components/Button.tsx
     src/components/Badge.tsx
     src/components/Avatar.tsx

   Page:
     app/dashboard/page.tsx — sidebar + stats + activity table

   All values from design tokens. Run the dev server to preview.
   ```

3. **Suggest next steps:**
   > "Run your dev server to preview. Then:
   > - `/chisel trace` another page
   > - `/chisel trim` to tweak the design system
   > - `/chisel check` to audit everything"

---

## Autonomous Decision Guidelines

| Situation | Default Decision |
|-----------|------------------|
| Component exists but doesn't use tokens | Use the existing component, don't rewrite it — `/chisel check` will catch token violations later |
| Component naming | Follow existing codebase conventions (PascalCase for React, kebab-case for Vue, etc.) |
| File location | Follow existing directory structure — don't create new conventions |
| Layout approach | CSS Grid for 2D layouts, Flexbox for 1D — never float |
| Responsive breakpoints | Mobile-first: `sm:`, `md:`, `lg:` — match framework defaults |
| Placeholder data | Realistic, diverse, not lorem ipsum |
| State management | Local state only — no global stores, no API calls |
| Animations | Follow ui-craft: ease-out entries, stagger 30-80ms, durations under 300ms |
| Missing tokens | If a value isn't in `system.md`, add it to `system.md` and `tokens.css` before using it |
| Multiple pages requested | Build one at a time, confirm each before starting the next |

---

## Red Flags — STOP and ask user if:

- No design system exists (`docs/design/system.md` missing)
- Page requires backend/API integration the user hasn't mentioned
- Page needs components that fundamentally conflict with the design system
- Requested layout doesn't work responsively without major trade-offs
- More than 8 new components needed — might indicate the page should be split
