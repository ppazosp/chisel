# Animation

Animation decisions, easing, timing, enter/exit patterns, springs, clip-path, and component patterns.

## The Decision Framework

Before writing any animation code, answer these questions in order:

### 1. Should this animate at all?

| Frequency | Decision |
| --- | --- |
| 100+ times/day (keyboard shortcuts, command palette) | No animation. Ever. |
| Tens of times/day (hover effects, list navigation) | Remove or drastically reduce |
| Occasional (modals, drawers, toasts) | Standard animation |
| Rare/first-time (onboarding, celebrations) | Can add delight |

**Never animate keyboard-initiated actions.** They're repeated hundreds of times daily.

### 2. What is the purpose?

Every animation must have a clear answer to "why does this animate?"

Valid purposes:
- **Spatial consistency**: toast enters and exits from the same direction
- **State indication**: a morphing button shows the state change
- **Explanation**: marketing animation showing how a feature works
- **Feedback**: button scales on press, confirming the interface heard
- **Preventing jarring changes**: elements appearing without transition feel broken

If the purpose is just "it looks cool" and users see it often, don't animate.

### 3. What easing?

```
Entering the screen?     → ease-out (starts fast, feels responsive)
Moving/morphing on screen? → ease-in-out (natural acceleration/deceleration)
Hover/color change?      → ease
Constant motion?         → linear
Default                  → ease-out
```

**Use custom curves.** Built-in CSS easings are too weak:

```css
--ease-out: cubic-bezier(0.23, 1, 0.32, 1);
--ease-in-out: cubic-bezier(0.77, 0, 0.175, 1);
--ease-drawer: cubic-bezier(0.32, 0.72, 0, 1);
```

**Never use ease-in for UI.** It starts slow — feels sluggish. A dropdown with `ease-in` at 300ms *feels* slower than `ease-out` at 300ms.

Resources: [easing.dev](https://easing.dev/), [easings.co](https://easings.co/)

### 4. How fast?

| Element | Duration |
| --- | --- |
| Button press feedback | 100-160ms |
| Tooltips, small popovers | 125-200ms |
| Dropdowns, selects | 150-250ms |
| Modals, drawers | 200-500ms |
| Marketing/explanatory | Can be longer |

**Rule: UI animations under 300ms.** A 180ms dropdown feels more responsive than a 400ms one.

## CSS Transitions vs. Keyframes

| | CSS Transitions | CSS Keyframes |
| --- | --- | --- |
| **Interruptible** | Yes — retargets mid-animation | No — restarts from beginning |
| **Use for** | Interactive state changes (hover, toggle, open/close) | One-shot sequences (enter animations, loading) |
| **Duration** | Adapts to remaining distance | Fixed regardless of state |

**Rule:** Always prefer CSS transitions for interactive elements. Reserve keyframes for one-shot sequences.

## Enter Animations: Split and Stagger

Don't animate a single container. Break content into semantic chunks:

1. **Split** into logical groups (title, description, buttons)
2. **Stagger** with 30-80ms delay between items
3. **For titles**, consider splitting into words with ~80ms stagger
4. **Combine** `opacity`, `blur`, and `translateY`

```tsx
// Motion — staggered enter
<motion.div
  initial="hidden"
  animate="visible"
  variants={{ visible: { transition: { staggerChildren: 0.05 } } }}
>
  <motion.h1
    variants={{
      hidden: { opacity: 0, y: 12, filter: "blur(4px)" },
      visible: { opacity: 1, y: 0, filter: "blur(0px)" },
    }}
  >
    Welcome
  </motion.h1>
  {/* ...more children */}
</motion.div>
```

```css
/* CSS-only stagger */
.stagger-item {
  opacity: 0;
  transform: translateY(12px);
  filter: blur(4px);
  animation: fadeInUp 300ms ease-out forwards;
}
.stagger-item:nth-child(1) { animation-delay: 0ms; }
.stagger-item:nth-child(2) { animation-delay: 50ms; }
.stagger-item:nth-child(3) { animation-delay: 100ms; }

@keyframes fadeInUp {
  to { opacity: 1; transform: translateY(0); filter: blur(0); }
}
```

Stagger is decorative — never block interaction while stagger animations play.

## Exit Animations

Exits should be softer than enters. The user's focus is moving on.

```css
/* Subtle exit — small fixed translateY */
.item-exit {
  opacity: 0;
  transform: translateY(-12px);
  transition: opacity 150ms ease-in, transform 150ms ease-in;
}
```

- Use small fixed `translateY` (e.g., `-12px`) instead of full height
- Exit duration shorter than enter (150ms vs 300ms)
- Don't remove exit animations entirely — subtle motion preserves context

### Asymmetric Enter/Exit Timing

Pressing should be slow when deliberate (hold-to-delete: 2s linear), release always snappy (200ms ease-out). Slow where the user is deciding, fast where the system responds.

## Never Animate from scale(0)

Nothing in the real world appears from nothing. Start from `scale(0.95)` or higher with opacity:

```css
/* Bad */
.entering { transform: scale(0); }

/* Good */
.entering { transform: scale(0.95); opacity: 0; }
```

## Scale on Press

`scale(0.97)` on `:active` for tactile feedback. Never below `0.95`.

```css
.button {
  transition: transform 160ms ease-out;
}
.button:active {
  transform: scale(0.97);
}
```

```tsx
// Tailwind
<button className="transition-transform duration-150 ease-out active:scale-[0.97]">

// Motion
<motion.button whileTap={{ scale: 0.97 }}>
```

### Static Prop Pattern

```tsx
const tapScale = "active:not-disabled:scale-[0.97]";

function Button({ static: isStatic, className, children, ...props }) {
  return (
    <button
      className={cn(
        "transition-transform duration-150 ease-out",
        !isStatic && tapScale,
        className,
      )}
      {...props}
    >
      {children}
    </button>
  );
}
```

## Contextual Icon Animations

Animate icons with `opacity`, `scale`, and `blur` — never toggle visibility. Use exactly these values:

- `scale`: `0.25` → `1`
- `opacity`: `0` → `1`
- `filter`: `blur(4px)` → `blur(0px)`
- `transition`: `{ type: "spring", duration: 0.3, bounce: 0 }` — **bounce must be `0`**

```tsx
// With Motion
<AnimatePresence mode="popLayout">
  <motion.span
    key={isActive ? "active" : "inactive"}
    initial={{ opacity: 0, scale: 0.25, filter: "blur(4px)" }}
    animate={{ opacity: 1, scale: 1, filter: "blur(0px)" }}
    exit={{ opacity: 0, scale: 0.25, filter: "blur(4px)" }}
    transition={{ type: "spring", duration: 0.3, bounce: 0 }}
  >
    <Icon />
  </motion.span>
</AnimatePresence>
```

Without Motion, keep both icons in the DOM (one absolute-positioned) and cross-fade with CSS transitions using `cubic-bezier(0.2, 0, 0, 1)`.

| Animate | Don't animate |
| --- | --- |
| Icons that appear on hover | Static navigation icons |
| State change icons (play → pause) | Decorative icons |
| Icons in contextual toolbars | Icons that are always visible |

## Origin-Aware Popovers

Popovers scale from their trigger, not center:

```css
.popover {
  transform-origin: var(--radix-popover-content-transform-origin);
}
```

**Exception: modals.** Modals keep `transform-origin: center` — they're not anchored to a trigger.

## Tooltips: Skip Delay on Subsequent Hovers

First tooltip delays. Once one is open, adjacent tooltips open instantly with no animation:

```css
.tooltip[data-instant] {
  transition-duration: 0ms;
}
```

## Skip Animation on Page Load

Use `initial={false}` on `AnimatePresence` to prevent enter animations on first render. Don't use it when the component relies on its `initial` prop for a first-time entrance (staggered heroes, loading states).

## Blur to Mask Imperfect Transitions

When a crossfade between two states feels off, add `filter: blur(2px)` during the transition. Blur bridges the visual gap by blending the two states together. Keep under 20px — heavy blur is expensive in Safari.

## @starting-style for CSS-Only Enter Animations

```css
.toast {
  opacity: 1;
  transform: translateY(0);
  transition: opacity 400ms ease, transform 400ms ease;

  @starting-style {
    opacity: 0;
    transform: translateY(100%);
  }
}
```

Replaces the `useEffect` + `setMounted(true)` pattern. Fall back to `data-mounted` attribute when browser support is needed.

## Spring Animations

Springs feel more natural — they simulate real physics and don't have fixed durations.

### When to use

- Drag interactions with momentum
- Elements that should feel "alive" (Dynamic Island style)
- Gestures that can be interrupted mid-animation
- Decorative mouse-tracking interactions

### Configuration

```js
// Apple-style (easier to reason about)
{ type: "spring", duration: 0.5, bounce: 0.2 }

// Physics-based (more control)
{ type: "spring", mass: 1, stiffness: 100, damping: 10 }
```

Keep bounce subtle (0.1-0.3). Avoid bounce in most UI contexts.

### Spring-based mouse interactions

Use `useSpring` to interpolate mouse position — direct binding feels artificial:

```jsx
const springRotation = useSpring(mouseX * 0.1, { stiffness: 100, damping: 10 });
```

Only for decorative interactions. Functional UI (banking graphs) should not animate.

### Interruptibility advantage

Springs maintain velocity when interrupted. CSS animations restart from zero. This makes springs ideal for gestures users might change mid-motion.

## clip-path for Animation

### The inset shape

`clip-path: inset(top right bottom left)` — each value "eats" into the element from that side.

```css
.hidden { clip-path: inset(0 100% 0 0); }
.visible { clip-path: inset(0 0 0 0); }
```

### Use cases

- **Tabs with color transitions**: Duplicate tab list, style copy as active, clip to active tab, animate clip on change
- **Hold-to-delete**: `inset(0 100% 0 0)` overlay, on `:active` transition to `inset(0 0 0 0)` over 2s linear, release snaps back 200ms ease-out
- **Image reveals on scroll**: Start `inset(0 0 100% 0)`, animate to `inset(0 0 0 0)` on viewport entry
- **Comparison sliders**: Clip top image, adjust inset based on drag position

## CSS Transforms

### translateY with percentages

Percentage values are relative to the element's own size. `translateY(100%)` moves by its own height. Prefer percentages over hardcoded pixels.

### scale() scales children

Unlike `width`/`height`, `scale()` scales children too. When scaling a button, content scales proportionally.

### 3D transforms

`rotateX()`, `rotateY()` with `transform-style: preserve-3d` for real 3D effects.

## Debugging Animations

- **Slow motion**: Temporarily 2-5x duration or use DevTools animation inspector
- **Frame-by-frame**: Chrome DevTools Animations panel
- **Real devices**: Connect phone via USB, test gestures on hardware
- **Next-day review**: Fresh eyes catch timing issues you missed during development
