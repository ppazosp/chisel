---
name: ui-craft
description: UI polish and micro-interaction rules for building interfaces that feel right. Use when building UI components, reviewing frontend code, implementing animations, hover states, shadows, borders, typography, gestures, micro-interactions, or any visual detail work. Triggers on UI polish, design details, "make it feel better", "feels off", animation decisions, easing, stagger, border radius, optical alignment, font smoothing, tabular numbers, image outlines, box shadows, drag interactions, scale on press, clip-path animations.
---

# UI Craft

Small details compound into great interfaces. Apply these principles when building or reviewing UI code.

## Quick Reference

| Category | When to Use |
| --- | --- |
| [Animation](animation.md) | Animation decisions, easing, timing, enter/exit, stagger, springs, clip-path, icons, scale-on-press |
| [Surfaces](surfaces.md) | Border radius, optical alignment, shadows, image outlines, hit areas |
| [Typography](typography.md) | Text wrapping, font smoothing, tabular numbers |
| [Gestures](gestures.md) | Drag, momentum dismissal, damping, pointer capture, friction |
| [Performance](performance.md) | Transition specificity, `will-change`, GPU compositing, CSS vs JS |

## Core Principles

### 1. Concentric Border Radius

Outer radius = inner radius + padding. Mismatched radii on nested elements is the most common thing that makes interfaces feel off. If padding > 24px, treat layers as separate surfaces.

### 2. Optical Over Geometric Alignment

When geometric centering looks off, align optically. Buttons with icons, play triangles, and asymmetric icons all need manual adjustment.

### 3. Shadows Over Borders

Layer multiple transparent `box-shadow` values for natural depth. Shadows adapt to any background; solid borders don't. Keep borders for dividers and layout separation.

### 4. Animate with Purpose

Before writing animation code, answer: (1) should this animate at all? (2) what is the purpose? (3) what easing? (4) how fast? If the only answer is "it looks cool" and users see it often, don't animate.

### 5. Interruptible Over Predetermined

CSS transitions for interactive state changes (hover, toggle, open/close) — they retarget mid-animation. Keyframes for one-shot sequences. Springs for gestures and drag.

### 6. Split and Stagger Enters

Don't animate a single container. Break content into semantic chunks and stagger each with 30-80ms delay between items. Keep it short — long delays feel slow.

### 7. Subtle Exits

Use a small fixed `translateY` instead of full height. Exit duration shorter than enter (150ms vs 300ms). The user's focus is moving on — don't fight for attention.

### 8. Never Animate from scale(0)

Nothing in the real world appears from nothing. Start from `scale(0.95)` or higher, combined with `opacity: 0`.

### 9. Scale on Press

`scale(0.97)` on `:active` gives buttons tactile feedback. Never below `0.95`. Use CSS transitions for interruptibility. Add a `static` prop to disable when motion would be distracting.

### 10. Ease-out for Entries, Never ease-in for UI

`ease-out` starts fast — feels responsive. `ease-in` starts slow — feels sluggish. Use custom curves, not browser defaults:

```css
--ease-out: cubic-bezier(0.23, 1, 0.32, 1);
--ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);
```

### 11. UI Animations Under 300ms

| Element | Duration |
| --- | --- |
| Button press | 100-160ms |
| Tooltips, small popovers | 125-200ms |
| Dropdowns, selects | 150-250ms |
| Modals, drawers | 200-500ms |

### 12. Font Smoothing

Apply `-webkit-font-smoothing: antialiased` to the root on macOS.

### 13. Tabular Numbers

`font-variant-numeric: tabular-nums` for any dynamically updating numbers.

### 14. Text Wrapping

`text-wrap: balance` on headings (max 6 lines). `text-wrap: pretty` for body text.

### 15. Image Outlines

Subtle `1px` outline with low opacity and `outline-offset: -1px` for consistent depth.

### 16. Never Use `transition: all`

Always specify exact properties. Tailwind's `transition-transform` covers `transform, translate, scale, rotate`.

### 17. Use `will-change` Sparingly

Only for `transform`, `opacity`, `filter`, `clip-path`. Never `all`. Only add when you see first-frame stutter.

### 18. Minimum Hit Area

Interactive elements need at least 44x44px (WCAG). Extend with pseudo-element if visible element is smaller. Never let hit areas overlap.

### 19. Origin-Aware Popovers

Popovers scale from their trigger (`transform-origin: var(--radix-popover-content-transform-origin)`). Exception: modals stay `transform-origin: center`.

### 20. Tooltips Skip Delay on Subsequent Hovers

First tooltip delays. Once one is open, adjacent tooltips open instantly with no animation.

### 21. Accessibility

Gate hover animations behind `@media (hover: hover) and (pointer: fine)`. Respect `prefers-reduced-motion` — reduce movement, keep opacity/color transitions.

## Common Mistakes

| Mistake | Fix |
| --- | --- |
| Same border radius on parent and child | `outerRadius = innerRadius + padding` |
| Icons look off-center | Adjust optically or fix SVG directly |
| Hard borders between sections | Use layered `box-shadow` with transparency |
| Jarring enter/exit animations | Split, stagger, exits softer than enters |
| Numbers cause layout shift | Apply `tabular-nums` |
| `transition: all` | Specify exact properties |
| `scale(0)` entry | Start from `scale(0.95)` with `opacity: 0` |
| `ease-in` on UI element | Switch to `ease-out` or custom curve |
| Animation on keyboard action | Remove animation entirely |
| Duration > 300ms on UI element | Reduce to 150-250ms |
| `transform-origin: center` on popover | Set to trigger location |
| Hover without media query | Add `@media (hover: hover) and (pointer: fine)` |
| Framer Motion `x`/`y` under load | Use `transform: "translateX()"` for GPU |

## Review Checklist

- [ ] Nested rounded elements use concentric border radius
- [ ] Icons are optically centered
- [ ] Shadows used instead of borders for depth (borders kept for dividers)
- [ ] Every animation has a clear purpose
- [ ] Enter animations split and staggered (30-80ms)
- [ ] Exit animations subtle and shorter than enters
- [ ] No `scale(0)` — minimum `scale(0.95)` with opacity
- [ ] Buttons use `scale(0.97)` on press where appropriate
- [ ] Easing is `ease-out` for entries, custom curves used
- [ ] UI animation durations under 300ms
- [ ] Dynamic numbers use `tabular-nums`
- [ ] Font smoothing applied at root
- [ ] Headings use `text-wrap: balance`
- [ ] Images have subtle outlines
- [ ] No `transition: all` — only specific properties
- [ ] `will-change` only on transform/opacity/filter, never `all`
- [ ] Hit areas at least 44x44px
- [ ] Popovers scale from trigger, modals from center
- [ ] Hover gated behind `@media (hover: hover) and (pointer: fine)`
- [ ] `prefers-reduced-motion` respected
- [ ] No animation on keyboard-initiated actions
- [ ] `AnimatePresence initial={false}` for default-state elements

## Review Format

When reviewing UI code, output a markdown table:

| Before | After | Why |
| --- | --- | --- |
| `transition: all 300ms` | `transition: transform 200ms ease-out` | Specify exact properties |
| `transform: scale(0)` | `transform: scale(0.95); opacity: 0` | Nothing appears from nothing |

## Reference Files

- [animation.md](animation.md) — Animation decisions, easing, timing, patterns, springs, clip-path
- [surfaces.md](surfaces.md) — Border radius, optical alignment, shadows, image outlines, hit areas
- [typography.md](typography.md) — Text wrapping, font smoothing, tabular numbers
- [gestures.md](gestures.md) — Drag, momentum, damping, pointer capture, friction
- [performance.md](performance.md) — Transition specificity, GPU compositing, CSS vs JS
