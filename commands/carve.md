---
description: Trace the design — build frontend pages using the design system
argument-hint: What to build (e.g. "dashboard with sidebar and stats", "settings page", "login form")
---

# Trace

Think deeply about a page, then build it fast. Understand the purpose, decompose into components, plan layout and interactions, then dispatch parallel agents to build each component simultaneously.

**Input:** $ARGUMENTS

---

## Phase 0: Pick Up the Chisel

**Goal:** Load everything before thinking

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

5. **Scan existing components:**
   - Glob for `src/components/**/*.{tsx,jsx,vue,svelte}`
   - Build a list of what already exists — names, props, variants

6. **If `$ARGUMENTS` is empty:** ask "What page do you want to build?"

---

## Phase 1: Study the Stone — Purpose

**Goal:** Understand why this page exists before anything else

**DO NOT write any code. Think first.**

1. **What is this page for?** What problem does it solve?
2. **Who uses it?** What role, what context, what mood?
3. **What's the primary action?** The ONE thing users come here to do.
4. **What's the user's mental model?** What do they expect to see when they arrive?

**Present:**
> "**Purpose:** [what and why]
> **Primary user:** [who]
> **Primary action:** [the main thing]
>
> Is this right?"

**Get confirmation.** Don't proceed until the WHY is clear.

---

## Phase 2: Study the Stone — Journey and Features

**Goal:** Map everything the user does on this page, step by step

1. **Walk through the user journey:**
   - User arrives → what do they see first?
   - What do they scan? What draws their eye?
   - What do they click/tap? What happens?
   - What are the secondary actions?
   - When are they done? Where do they go next?

2. **List every feature as interaction + outcome:**
   - "Click stat card → filters table to that category"
   - "Type in search → filters rows in real time"
   - "Click sort header → re-sorts by that column"

3. **List page states:**
   - Empty (no data yet)
   - Loading (fetching)
   - Populated (normal use)
   - Error (something broke)
   - What does each state look like?

---

## Phase 3: Study the Stone — Information and Data

**Goal:** What data exists on this page and what's its hierarchy

1. **Information hierarchy** — rank by importance:
   - **Primary:** the most critical data (what users came for)
   - **Secondary:** supporting context (helps interpret primary)
   - **Tertiary:** metadata, timestamps, labels
   - **Actions:** buttons, links, controls

2. **Data shapes:**
   - Define TypeScript types/interfaces for every data entity on the page
   - What fields? What types? What's optional?
   - Where does the data come from? (API, local state, URL params)

3. **Placeholder data:**
   - Realistic values — diverse names, varied numbers, recent dates
   - Not "John Doe", not round numbers, not lorem ipsum

---

## Phase 4: Mark the Lines — Layout

**Goal:** Where does everything go on the screen

1. **Draw the layout as a text diagram:**
   ```
   ┌──────────────────────────────────────────┐
   │ Header                                    │
   ├────────────┬─────────────────────────────┤
   │            │ StatsGrid                    │
   │  Sidebar   │  ┌──────┐┌──────┐┌──────┐  │
   │            │  │Stat  ││Stat  ││Stat  │  │
   │            │  └──────┘└──────┘└──────┘  │
   │            ├─────────────────────────────┤
   │            │ ActivityTable               │
   │            │  ┌─────────────────────────┐│
   │            │  │ ActivityRow              ││
   │            │  │ ActivityRow              ││
   │            │  └─────────────────────────┘│
   └────────────┴─────────────────────────────┘
   ```

2. **Specify for each level:**
   - CSS Grid or Flexbox
   - Gap/spacing (from token scale)
   - Alignment

3. **Responsive behavior:**
   - How does it collapse on mobile?
   - What stacks? What hides? What shrinks?
   - Breakpoints (mobile-first: `sm:`, `md:`, `lg:`)

---

## Phase 5: Mark the Lines — Components

**Goal:** Now that layout is known, identify every component needed

1. **Walk through the layout diagram and name every piece:**
   - Every distinct visual element = component
   - Every repeated pattern = component
   - Every interactive piece = component

2. **Classify each:**

   | Classification | Meaning | Example |
   |---|---|---|
   | **Exists** | Already in codebase, reuse | Button, Badge, Avatar |
   | **Exists in system** | In system.md but not built yet | Card, Input, Select |
   | **New leaf** | Atomic, no children | StatsCard, ActivityRow |
   | **New composite** | Contains other components | StatsGrid, ActivityTable |
   | **Page layout** | The page shell | DashboardPage |

3. **For each NEW component, define:**
   - **Props:** what data it accepts (from the types defined in Phase 3)
   - **Variants:** size/style variants
   - **States:** default, hover, active, disabled, empty, loading, error
   - **Tokens used:** which design tokens it consumes

---

## Phase 6: Mark the Lines — Interactions and State

**Goal:** How do the pieces talk to each other

1. **Interaction map:**
   ```
   Sidebar nav click     → updates activeSection (page state) → highlights nav, scrolls content
   StatsCard click       → updates filter (page state) → ActivityTable re-filters
   Sort header click     → updates sortKey (table state) → rows re-sort locally
   Search input onChange  → updates query (page state) → ActivityTable filters
   ```

2. **State ownership:**
   - What state lives at page level? (shared between components)
   - What state lives inside a component? (local only)
   - What gets passed down as props vs managed internally?

3. **State flow diagram:**
   ```
   DashboardPage (owns: activeSection, filter, searchQuery)
   ├── Sidebar (receives: activeSection, emits: onNavigate)
   ├── StatsGrid (receives: data, emits: onStatClick → sets filter)
   │   └── StatsCard (receives: stat, onClick)
   └── ActivityTable (receives: data, filter, searchQuery)
       └── ActivityRow (receives: activity)
   ```

---

## Phase 7: Step Back — Confirm the Plan

**Goal:** One final checkpoint before building

**Present everything:**

> **Purpose:** [from Phase 1]
>
> **Layout:**
> [diagram from Phase 4]
>
> **Components to build (N):**
> - [name] (leaf/composite) — [one-line description]
> - ...
>
> **Reusing (N):**
> - [name] — already exists
>
> **State flow:**
> [diagram from Phase 6]
>
> **Build plan:**
> - Wave 1: [leaf components] — parallel
> - Wave 2: [composites] — parallel
> - Wave 3: page assembly
>
> Ready to build?

**Get user confirmation.** This is the last checkpoint before parallel build.

---

## Phase 8: Strike — Build in Parallel

**Goal:** Dispatch one agent per component — all leaf components simultaneously, then composites, then page

**CRITICAL RULES for every agent:**
- All design values from tokens — no hardcoded colors, spacing, radius, shadows
- Follow component patterns from `system.md`
- Apply `ui-craft` principles:
  - Concentric border radius on nested elements
  - Shadows over borders for depth
  - Optical alignment on icons
  - `tabular-nums` on dynamic numbers
  - `text-wrap: balance` on headings
  - Scale `0.97` on press for interactive elements
  - `transition-property` specific, never `all`
  - Hit areas at least 44x44px
  - `ease-out` for entries, custom curves
  - `prefers-reduced-motion` respected
  - Hover gated behind `@media (hover: hover) and (pointer: fine)`
- One file per component
- Include all states: default, hover, active, focus-visible, disabled
- Realistic placeholder data

**Build order — three waves:**

### Wave 1: Leaf components (parallel)

Dispatch one agent per leaf component simultaneously. Each agent receives:
- Component name, props, variants, states, interactions
- Full `system.md` content
- Framework context (React/Vue/Svelte, Tailwind yes/no)
- The ui-craft rules above
- File path to create

```
Example with 4 leaf components:

Agent("Build StatsCard.tsx — [full spec + system.md + ui-craft rules]")
Agent("Build ActivityRow.tsx — [full spec + system.md + ui-craft rules]")
Agent("Build NavItem.tsx — [full spec + system.md + ui-craft rules]")
Agent("Build SearchInput.tsx — [full spec + system.md + ui-craft rules]")

All 4 run simultaneously.
```

**Batch size:** max 8 parallel agents. If more than 8 leaf components, batch into groups.

**Wait for all Wave 1 agents to complete before Wave 2.**

### Wave 2: Composite components (parallel)

Dispatch one agent per composite component. Each agent receives everything from Wave 1 plus:
- The file paths of child components created in Wave 1
- How children are arranged (layout spec from Phase 3)
- Interaction map — what state this component manages

```
Agent("Build StatsGrid.tsx — uses StatsCard, responsive grid — [spec]")
Agent("Build ActivityTable.tsx — uses ActivityRow, sortable — [spec]")
Agent("Build Sidebar.tsx — uses NavItem, sticky — [spec]")

All 3 run simultaneously.
```

**Wait for all Wave 2 agents to complete before Wave 3.**

### Wave 3: Page assembly (single agent)

One agent assembles the page. It receives:
- All component file paths from Wave 1 + Wave 2
- Layout diagram from Phase 3
- Interaction map — page-level state, component communication
- Data types and placeholder data
- Routing/framework conventions

The page agent:
1. Creates the page file in the framework's expected location
2. Imports all components
3. Defines page-level state and data
4. Wires up inter-component interactions
5. Implements responsive layout
6. Adds placeholder data with realistic values

---

## Phase 9: Sand — Polish Pass

**Goal:** Review everything just built against ui-craft

**Review every component and page file against the checklist:**

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
- [ ] Component communication works as planned
- [ ] Responsive layout collapses correctly

**Fix any violations.**

---

## Phase 10: Inspect — Verify and Report

**Goal:** Make sure it runs

**Actions:**

1. **Run lint/typecheck** if available — fix errors
2. **Report what was built:**
   ```
   Built: Dashboard page

   Wave 1 (leaf components, built in parallel):
     src/components/StatsCard.tsx — stat with icon, value, label, trend
     src/components/ActivityRow.tsx — row with avatar, action, timestamp
     src/components/NavItem.tsx — sidebar navigation item
     src/components/SearchInput.tsx — search with icon and clear button

   Wave 2 (composite components, built in parallel):
     src/components/StatsGrid.tsx — responsive grid of StatsCards
     src/components/ActivityTable.tsx — sortable table of ActivityRows
     src/components/Sidebar.tsx — sticky nav with user avatar

   Wave 3 (page assembly):
     app/dashboard/page.tsx — full layout with interactions

   Reused: Button, Badge, Avatar

   All values from design tokens. Run the dev server to preview.
   ```

3. **Suggest next steps:**
   > "Run your dev server to preview. Then:
   > - `/chisel carve` another page
   > - `/chisel hone` to tweak the design system
   > - `/chisel inspect` to audit everything"

---

## Autonomous Decision Guidelines

| Situation | Default Decision |
|-----------|------------------|
| Component exists but doesn't use tokens | Reuse it — `/chisel inspect` will catch violations later |
| Component naming | Follow codebase conventions (PascalCase React, kebab-case Vue) |
| File location | Follow existing directory structure |
| Layout approach | CSS Grid for 2D, Flexbox for 1D — never float |
| Responsive | Mobile-first with `sm:`, `md:`, `lg:` breakpoints |
| Placeholder data | Realistic, diverse, varied numbers |
| State management | Local state only — no global stores, no API calls |
| Animations | ui-craft: ease-out entries, stagger 30-80ms, under 300ms |
| Missing tokens | Add to `system.md` and `tokens.css` before using |
| Multiple pages | Build one at a time, confirm each |
| Component needs >5 props | Consider splitting into smaller components |
| Shared state between components | Lift to nearest common parent, pass down |

---

## Red Flags — STOP and ask user if:

- No design system exists
- Page requires backend/API integration not mentioned
- Components fundamentally conflict with the design system
- Layout doesn't work responsively
- More than 12 new components — page should be split
- Interaction map has circular dependencies
