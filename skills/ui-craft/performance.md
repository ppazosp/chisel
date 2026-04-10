# Performance

Transition specificity, GPU compositing, and CSS vs JS animation performance.

## Only Animate Transform and Opacity

These properties skip layout and paint, running on the GPU. Animating `padding`, `margin`, `height`, or `width` triggers all three rendering steps.

GPU-compositable properties: `transform`, `opacity`, `filter`, `clip-path`.

## Never Use `transition: all`

Always specify exact properties:

```css
/* Good */
.button {
  transition-property: scale, background-color;
  transition-duration: 150ms;
  transition-timing-function: ease-out;
}

/* Bad */
.button {
  transition: all 150ms ease-out;
}
```

```tsx
// Good — explicit
<button className="transition-[scale,background-color] duration-150 ease-out">

// Bad — transition all
<button className="transition duration-150 ease-out">
```

### Tailwind `transition-transform` Note

Maps to `transition-property: transform, translate, scale, rotate` — covers all transform-related properties. For multiple non-transform properties, use bracket syntax: `transition-[scale,opacity,filter]`.

## Use `will-change` Sparingly

Hints the browser to pre-promote to a GPU compositing layer. Without it, the one-time layer promotion on first frame can cause micro-stutter.

### Rules

```css
/* Good — specific GPU-compositable property */
.animated-card { will-change: transform; }
.animated-card { will-change: transform, opacity; }

/* Bad */
.animated-card { will-change: all; }
.animated-card { will-change: background-color, padding; }
```

| Property | GPU-compositable | Worth `will-change` |
| --- | --- | --- |
| `transform` | Yes | Yes |
| `opacity` | Yes | Yes |
| `filter` | Yes | Yes |
| `clip-path` | Yes | Yes |
| `top`, `left`, `width`, `height` | No | No |
| `background`, `border`, `color` | No | No |

Only add when you see first-frame stutter — Safari benefits most. Each extra compositing layer costs memory.

## CSS Variables Are Inheritable

Changing a CSS variable on a parent recalculates styles for all children. In a drawer with many items, updating `--swipe-amount` on the container causes expensive style recalculation.

```js
// Bad: triggers recalc on all children
element.style.setProperty('--swipe-amount', `${distance}px`);

// Good: only affects this element
element.style.transform = `translateY(${distance}px)`;
```

## Framer Motion GPU Caveat

Framer Motion's shorthand properties (`x`, `y`, `scale`) are NOT hardware-accelerated. They use `requestAnimationFrame` on the main thread.

```jsx
// NOT hardware accelerated — drops frames under load
<motion.div animate={{ x: 100 }} />

// Hardware accelerated — stays smooth
<motion.div animate={{ transform: "translateX(100px)" }} />
```

This matters when the browser is loading content, running scripts, or painting simultaneously.

## CSS Animations Beat JS Under Load

CSS animations run off the main thread. When the browser is busy, Framer Motion animations (using `requestAnimationFrame`) drop frames. CSS animations remain smooth.

Use CSS for predetermined animations; JS for dynamic, interruptible ones.

## Web Animations API (WAAPI)

JavaScript control with CSS performance. Hardware-accelerated, interruptible, no library needed:

```js
element.animate(
  [
    { clipPath: 'inset(0 0 100% 0)' },
    { clipPath: 'inset(0 0 0 0)' }
  ],
  {
    duration: 1000,
    fill: 'forwards',
    easing: 'cubic-bezier(0.77, 0, 0.175, 1)',
  }
);
```
