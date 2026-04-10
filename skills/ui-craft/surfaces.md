# Surfaces

Border radius, optical alignment, shadows, image outlines, and hit areas.

## Concentric Border Radius

When nesting rounded elements, the outer radius must equal the inner radius plus the padding:

```
outerRadius = innerRadius + padding
```

If padding > 24px, treat layers as separate surfaces — don't force concentric math.

### Example

```css
/* Good — concentric radii */
.card {
  border-radius: 20px; /* 12 + 8 */
  padding: 8px;
}
.card-inner {
  border-radius: 12px;
}

/* Bad — same radius on both */
.card { border-radius: 12px; padding: 8px; }
.card-inner { border-radius: 12px; }
```

```tsx
// Tailwind
// Good — outer radius accounts for padding
<div className="rounded-2xl p-2">       {/* 16px radius, 8px padding */}
  <div className="rounded-lg">          {/* 8px radius = 16 - 8 */}
    ...
  </div>
</div>

// Bad — same radius
<div className="rounded-xl p-2">
  <div className="rounded-xl">...</div>
</div>
```

## Optical Alignment

When geometric centering looks off, align optically.

### Buttons with Text + Icon

Less padding on the icon side. Rule of thumb: `icon-side padding = text-side padding - 2px`.

```css
.button-with-icon {
  padding-left: 16px;
  padding-right: 14px; /* icon side */
}
```

```tsx
<button className="pl-4 pr-3.5 flex items-center gap-2">
  <span>Continue</span>
  <ArrowRightIcon />
</button>
```

### Play Button Triangles

Triangles' geometric center ≠ visual center. Shift right:

```css
.play-button svg {
  margin-left: 2px;
}
```

### Asymmetric Icons (Stars, Arrows, Carets)

Best fix: adjust the SVG directly so no extra margin is needed. Fallback: `margin-left: 1px`.

## Shadows Instead of Borders

For **buttons, cards, and containers** that use a border for depth, replace with `box-shadow`. Shadows adapt to any background via transparency.

**Do not apply to dividers** (`border-b`, `border-t`, side borders) or layout-separation borders. Those stay as borders.

### Shadow as Border

```css
/* Light mode — three layers: ring, lift, ambient */
:root {
  --shadow-border:
    0px 0px 0px 1px rgba(0, 0, 0, 0.06),
    0px 1px 2px -1px rgba(0, 0, 0, 0.06),
    0px 2px 4px 0px rgba(0, 0, 0, 0.04);
  --shadow-border-hover:
    0px 0px 0px 1px rgba(0, 0, 0, 0.08),
    0px 1px 2px -1px rgba(0, 0, 0, 0.08),
    0px 2px 4px 0px rgba(0, 0, 0, 0.06);
}

/* Dark mode — single white ring */
--shadow-border: 0 0 0 1px rgba(255, 255, 255, 0.08);
--shadow-border-hover: 0 0 0 1px rgba(255, 255, 255, 0.13);
```

### Usage with Hover

```css
.card {
  box-shadow: var(--shadow-border);
  transition-property: box-shadow;
  transition-duration: 150ms;
  transition-timing-function: ease-out;
}
.card:hover {
  box-shadow: var(--shadow-border-hover);
}
```

### When to Use Which

| Use shadows | Use borders |
| --- | --- |
| Cards, containers with depth | Dividers between list items |
| Buttons with bordered styles | Table cell boundaries |
| Elevated elements (dropdowns, modals) | Form input outlines (accessibility) |
| Elements on varied backgrounds | Hairline separators in dense UI |

## Image Outlines

Subtle `1px` outline with low opacity for consistent depth:

```css
/* Light */
img {
  outline: 1px solid rgba(0, 0, 0, 0.1);
  outline-offset: -1px;
}

/* Dark */
img {
  outline: 1px solid rgba(255, 255, 255, 0.1);
  outline-offset: -1px;
}
```

```tsx
<img className="outline outline-1 -outline-offset-1 outline-black/10 dark:outline-white/10" />
```

`outline` doesn't affect layout. `outline-offset: -1px` keeps it inset.

## Minimum Hit Area

Interactive elements need at least 44x44px hit area (WCAG). Extend with pseudo-element:

```css
.checkbox {
  position: relative;
  width: 20px;
  height: 20px;
}
.checkbox::after {
  content: "";
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 44px;
  height: 44px;
}
```

```tsx
<button className="relative size-5 after:absolute after:top-1/2 after:left-1/2 after:size-11 after:-translate-1/2">
  <CheckIcon />
</button>
```

If the extended hit area overlaps another interactive element, shrink the pseudo-element — but make it as large as possible without colliding.
