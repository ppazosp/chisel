---
description: Audit all frontend code against the design system
---

# Review

Scan all frontend code for design system violations. Report issues, offer auto-fix.

---

## Phase 0: Load Design System

**Goal:** Read current state

**Actions:**

1. **Check for `docs/design/system.md`:**
   - If missing: STOP — "No design system found. Run `/stencil cut` first."

2. **Read `docs/design/system.md`** fully — all tokens and component patterns

3. **Read `docs/design/tokens.css`** — the canonical CSS values

---

## Phase 1: Scan Frontend Files

**Goal:** Find all files that could contain design values

**Actions:**

1. **Glob for frontend files:**
   - `src/**/*.{tsx,jsx,vue,svelte,html}`
   - `src/**/*.{css,scss,less}`
   - `app/**/*.{tsx,jsx,vue,svelte}`
   - `pages/**/*.{tsx,jsx,vue,svelte}`
   - `components/**/*.{tsx,jsx,vue,svelte}`

2. **Exclude:**
   - `node_modules/`
   - `docs/design/` (the system itself)
   - Build output (`dist/`, `.next/`, `build/`)
   - Test files (`*.test.*`, `*.spec.*`, `__tests__/`)

---

## Phase 2: Detect Violations

**Goal:** Check every file against the design system

**For each file, check:**

### Color violations
- Hardcoded hex/rgb/hsl values that match or approximate a token
- Hardcoded colors that should be tokens but aren't
- `bg-red-500` style Tailwind classes when `bg-destructive` is defined
- Opacity values applied to colors that differ from token patterns

### Spacing violations
- Hardcoded pixel values for margin/padding that aren't on the spacing scale
- Tailwind classes using arbitrary values (`p-[13px]`) when a token exists (`p-3`)
- Inconsistent gap/margin/padding between similar components

### Typography violations
- Font families not matching `--font-sans`/`--font-display`/`--font-mono`
- Font sizes not on the type scale
- Font weights outside the defined set
- Missing `tabular-nums` on dynamic number displays

### Shape violations
- Border radius values not matching any `--radius-*` token
- Nested elements with non-concentric border radius
- Inconsistent radius between similar components

### Shadow violations
- `box-shadow` values that don't match any `--shadow-*` token
- Hardcoded shadows when tokens are available
- Borders used for depth where shadow tokens should be used

### Animation violations
- `transition: all` anywhere
- Durations outside the defined scale
- Easing values not matching tokens
- Missing `prefers-reduced-motion` on animated elements

### Component pattern violations
- Components that don't follow the patterns defined in system.md
- Inconsistent state styling (hover, active, disabled) across similar components

---

## Phase 3: Report

**Goal:** Present violations clearly with fix suggestions

**Output format:**

```markdown
## Design System Audit

**Files scanned:** N
**Violations found:** N (N critical, N warning)

### Critical — Hardcoded values that MUST use tokens

| File | Line | Issue | Current | Should be |
|------|------|-------|---------|-----------|
| `src/components/Button.tsx` | 12 | Hardcoded color | `#3b82f6` | `var(--color-primary)` |
| `src/components/Card.tsx` | 8 | Hardcoded radius | `12px` | `var(--radius-lg)` |

### Warnings — Inconsistencies

| File | Line | Issue | Details |
|------|------|-------|---------|
| `src/components/Modal.tsx` | 23 | Non-concentric radius | outer=16px, inner=12px, padding=8px → outer should be 20px |
| `src/pages/Dashboard.tsx` | 45 | Off-scale spacing | `gap: 18px` — nearest token: `--space-4` (16px) or `--space-6` (24px) |

### Clean
[List of files with no violations]
```

---

## Phase 4: Auto-Fix

**Goal:** Fix violations with user approval

**Actions:**

1. **Ask:**
   > "Found N violations. Auto-fix all? Or review one by one?"

2. **If "fix all":**
   - For each violation, apply the fix
   - Process one file at a time
   - Replace hardcoded values with token references
   - Fix concentric radius issues
   - Replace `transition: all` with specific properties
   - Run lint/typecheck after all fixes

3. **If "review one by one":**
   - Present each violation with the proposed fix
   - User approves or skips each one

4. **After fixes:**
   - Regenerate showcase to confirm visual consistency
   - Report what was fixed
   - Commit: `fix[design]: resolve design system violations`

---

## Autonomous Decision Guidelines

| Situation | Default Decision |
|-----------|------------------|
| Hardcoded value close to token | Suggest the nearest token — "Did you mean `--space-4` (16px) instead of `15px`?" |
| Value doesn't match any token | Flag as warning, not critical — might be intentional |
| File uses no tokens at all | Might not be a UI file — flag but don't auto-fix |
| Third-party component styles | Skip — can't control external components |
| CSS-in-JS with dynamic values | Flag hardcoded parts, skip computed values |
