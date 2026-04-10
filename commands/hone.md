---
description: Tweak design tokens — preview in showcase, then propagate to all components
argument-hint: What to change (e.g. "warmer palette", "rounder buttons", "smaller text")
---

# Refine

Change design tokens, preview the impact in the showcase, and propagate approved changes to all component files.

**Input:** $ARGUMENTS

---

## Phase 0: Load Design System

**Goal:** Read current state

**Actions:**

1. **Check for `docs/design/system.md`:**
   - If missing: STOP — "No design system found. Run `/chisel cut` first."

2. **Read `docs/design/system.md`** fully — all tokens, direction, component patterns

3. **If `$ARGUMENTS` is empty:** ask "What do you want to change?"

---

## Phase 1: Interpret Change

**Goal:** Translate the user's request into specific token updates

**Process:**

1. **Classify the request:**

   | Type | Examples | Action |
   |---|---|---|
   | **Concrete** | "rounder buttons", "darker background", "change primary to blue" | Skip research, map directly to tokens |
   | **Stylistic** | "make it look like Linear", "brutalist", "more playful", "warmer" | Research first, then map to tokens |

2. **If stylistic — research online (ALWAYS):**
   - Use WebSearch to research what the user means concretely
   - Search for: `"<style> UI design tokens"`, `"<style> color palette"`, `"<style> design system"`
   - If it references a product ("like Linear", "like Notion"): research that product's specific design language — colors, fonts, radius, shadows
   - Extract concrete token values from the research
   - Use these to inform the changes, don't guess

3. **If stylistic — check for Stitch MCP (after research):**
   - Use `ToolSearch` with query `"stitch"` to check if Stitch MCP tools are available
   - If not available: recommend installing it — "Stitch MCP generates visual mockups from text — highly recommended for better design quality. Install at https://stitch.withgoogle.com/docs/mcp/setup"
   - If available, offer: "Want me to generate mockups with the new direction in Stitch? This significantly improves design quality."
   - If user says yes:
     - Load Stitch tool schemas via ToolSearch
     - Feed research findings into the prompt
     - Use `mcp__stitch__generate_screen_from_text` to generate 1-2 variants applying the new style
     - Present to user, combine research + Stitch output to inform token changes
   - If user says no, or Stitch not available → proceed with research findings only

4. **Map request to token changes:**

   | Request | Tokens affected |
   |---|---|
   | "warmer palette" | All `--color-*` tokens shift warm |
   | "rounder buttons" | `--radius-md`, `--radius-lg` increase |
   | "darker background" | `--color-background`, `--color-surface` |
   | "more contrast" | `--color-text-*` values, border opacity |
   | "smaller text" | `--text-*` scale values |
   | "change primary to blue" | `--color-primary`, `--color-primary-hover` |
   | "less shadow" | `--shadow-*` values reduce |
   | "tighter spacing" | `--space-*` scale compresses |

4. **Present the planned changes as a diff:**
   ```
   Token changes:
     --color-primary:       #6366f1 → #3b82f6
     --color-primary-hover: #4f46e5 → #2563eb
     --radius-md:           8px → 12px
   ```

5. **Ask: "Apply these changes and regenerate showcase?"**
   - If user adjusts: recalculate and re-present
   - If user confirms: proceed

---

## Phase 2: Update System Files

**Goal:** Apply token changes to system.md and regenerate tokens.css

**Actions:**

1. **Update `docs/design/system.md`:**
   - Change the specific token values in their tables
   - Update the `Last updated` date
   - If the change affects component patterns (e.g., radius change means button pattern description needs updating), update those too

2. **Regenerate `docs/design/tokens.css`:**
   - Rebuild the full CSS file from updated system.md
   - Ensure light/dark mode values are consistent

3. **If Tailwind is detected and config was extended during `/chisel cut`:**
   - Check if any NEW tokens were added that need Tailwind mappings
   - Update tailwind config if needed (existing var references don't need changes — they resolve dynamically)

---

## Phase 3: Regenerate Showcase

**Goal:** Preview the changes

**Actions:**

1. Regenerate `docs/design/showcase.html` with updated tokens
2. Open it:
   ```bash
   open docs/design/showcase.html
   ```

3. **Ask:**
   > "Showcase updated. Review in your browser.
   >
   > Options:
   > - **'looks good'** → propagate changes to all components
   > - **'tweak more'** → describe what else to change
   > - **'revert'** → undo these changes"

4. **If "tweak more":** go back to Phase 1 with the new request
5. **If "revert":** restore system.md and tokens.css from git, regenerate showcase
6. **If approved:** proceed to Phase 4

---

## Phase 4: Propagate to Components

**Goal:** Update all component files to reflect the new tokens — in parallel

**Actions:**

1. **Scan for component files:**
   - Glob for `src/components/**/*.{tsx,jsx,vue,svelte,html,css}`
   - Also check `src/app/**/*`, `src/pages/**/*` for inline styles
   - Filter out files with no design values (no colors, spacing, radius, shadows)

2. **Prepare the shared context** (same for every agent):
   - Full `system.md` content
   - Token changes with old → new values
   - Framework context (React/Vue/Svelte, Tailwind yes/no)

3. **Dispatch propagator subagents in parallel:**
   - Launch ONE propagator agent PER component file
   - Each agent receives: file path + shared context
   - Each agent edits only its assigned file — no cross-file dependencies
   - Use the Agent tool with multiple parallel calls in a single message

   ```
   Example with 4 components:
   
   Agent(propagator, "Update src/components/Button.tsx — [context]")
   Agent(propagator, "Update src/components/Card.tsx — [context]")
   Agent(propagator, "Update src/components/Input.tsx — [context]")
   Agent(propagator, "Update src/components/Modal.tsx — [context]")
   
   All 4 run simultaneously.
   ```

   **Batch size:** If more than 8 component files, batch into groups of 8 parallel agents. Wait for each batch to complete before launching the next.

4. **After all agents return:**
   - Collect results from each propagator
   - Run the project's lint/typecheck if available
   - Report what was changed:
     ```
     Propagated to 12 components (3 batches of 4):
       src/components/Button.tsx — updated radius, primary color
       src/components/Card.tsx — updated shadow, surface color
       src/components/Input.tsx — no changes needed
       ...
     ```

5. **Commit:** `feat[design]: refine design tokens — <brief description of change>`

---

## Autonomous Decision Guidelines

| Situation | Default Decision |
|-----------|------------------|
| Ambiguous change | Ask for clarification — "warmer" could mean many things |
| Change affects contrast ratio | Verify WCAG AA compliance, warn if not met |
| Change to primary color | Also update primary-hover (darken 10%) |
| Change to background | Also check surface, border values for consistency |
| Change to radius | Keep concentric radius relationships intact |
| Component uses hardcoded value | Replace with token reference during propagation |
| File has no design tokens | Skip — not a UI component |
