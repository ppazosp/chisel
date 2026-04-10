---
description: Trace the design — build frontend pages using the design system
argument-hint: What to build (e.g. "dashboard with sidebar and stats", "settings page", "login form")
---

# Trace

Think deeply about a page, then build it fast. Understand the purpose, decompose into components, plan layout and interactions, then dispatch parallel agents to build each component simultaneously.

**Input:** $ARGUMENTS

---

## Phase 0: Load Context

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

## Phase 1: Understand the Page

**Goal:** Think about WHAT this page is before HOW to build it

**DO NOT write any code yet. Think first.**

**Process:**

1. **Purpose:**
   - What is this page for? What problem does it solve?
   - Who uses it? What's the primary action they take here?
   - What information is most important on this page?
   - What's the user's mental model when they arrive here?

2. **Features:**
   - What does the user DO on this page? List every interaction.
   - What data does the page display? Where does it come from?
   - What states does the page have? (empty, loading, populated, error)
   - What happens on success? On failure?

3. **Present your understanding:**
   > "Here's how I understand this page:
   >
   > **Purpose:** [what and why]
   > **Primary action:** [the main thing users do here]
   > **Key information:** [what's most prominent]
   > **Features:**
   > - [feature 1 — interaction + outcome]
   > - [feature 2 — interaction + outcome]
   > **States:** [empty, loaded, error, etc.]
   >
   > Is this right? Anything missing?"

4. **Get user confirmation.** Don't proceed until the purpose is clear.

---

## Phase 2: Decompose into Components

**Goal:** Break the page into a component tree — every piece identified and specced

**Process:**

1. **Identify every component the page needs:**
   - Start from the outermost layout and work inward
   - Every distinct visual element is a component
   - Every repeated pattern is a component
   - Every interactive piece is a component

2. **Classify each component:**

   | Classification | Meaning | Example |
   |---|---|---|
   | **Exists** | Already in codebase, reuse as-is | Button, Badge, Avatar |
   | **Exists in system** | Defined in system.md showcase but not yet built | Card, Input, Select |
   | **New leaf** | New atomic component, no children | StatsCard, ActivityRow |
   | **New composite** | New component composed of other components | StatsGrid, ActivityTable |
   | **Page layout** | The page shell — arranges everything | DashboardPage |

3. **For each NEW component, define:**
   - **Props:** what data it accepts
   - **Variants:** size/style variants if any
   - **States:** default, hover, active, disabled, empty, loading, error
   - **Interactions:** what happens on click, hover, focus
   - **Tokens used:** which design tokens it consumes
   - **Children:** what components it contains (for composites)

---

## Phase 3: Plan Layout and Interactions

**Goal:** Define how components are arranged and how they talk to each other

**Process:**

1. **Layout plan:**
   - Draw the structure as a text diagram:
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
     │            │  │ ActivityRow              ││
     │            │  └─────────────────────────┘│
     └────────────┴─────────────────────────────┘
     ```
   - Specify: CSS Grid or Flexbox for each level
   - Specify: responsive behavior (how it collapses on mobile)

2. **Interaction map:**
   - Which components trigger state changes in other components?
   - What state lives where? (page-level vs component-level)
   - Navigation flows (click sidebar item → filter table? switch view?)
   - Any shared state between components?

   ```
   Interactions:
     Sidebar nav click → updates active page, highlights nav item
     StatsCard click → filters ActivityTable to that stat's category
     ActivityTable sort header → re-sorts rows locally
     Search input → filters ActivityTable rows
   ```

3. **Data plan:**
   - What placeholder data structure does each component need?
   - Define TypeScript types/interfaces for the data shapes
   - Realistic placeholder values (not lorem ipsum, not "John Doe")

4. **Present the full plan:**
   > "Here's the build plan:
   >
   > **Component tree:**
   > [diagram]
   >
   > **New components to build (6):**
   > - StatsCard (leaf) — icon, value, label, trend
   > - StatsGrid (composite) — responsive grid of StatsCards
   > - ActivityRow (leaf) — avatar, description, timestamp, badge
   > - ActivityTable (composite) — header + list of ActivityRows
   > - Sidebar (composite) — nav links, user avatar
   > - DashboardPage (layout) — arranges everything
   >
   > **Reusing (3):**
   > - Button, Badge, Avatar (already exist)
   >
   > **Interactions:**
   > [interaction map]
   >
   > Ready to build?"

5. **Get user confirmation.** This is the last checkpoint before parallel build.

---

## Phase 4: Build Components in Parallel

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

## Phase 5: Polish Pass

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

## Phase 6: Verify and Report

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
   > - `/chisel trace` another page
   > - `/chisel trim` to tweak the design system
   > - `/chisel check` to audit everything"

---

## Autonomous Decision Guidelines

| Situation | Default Decision |
|-----------|------------------|
| Component exists but doesn't use tokens | Reuse it — `/chisel check` will catch violations later |
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
