# Typography

Text wrapping, font smoothing, and tabular numbers.

## Text Wrapping

### text-wrap: balance

Distributes text evenly across lines, preventing orphaned words. **Only works on 6 lines or fewer** (Chromium) or 10 lines (Firefox).

```css
h1, h2, h3 {
  text-wrap: balance;
}
```

**Tailwind:** `text-balance`

Don't use on long paragraphs — it's silently ignored past the line limit.

### text-wrap: pretty

Optimizes the last line to avoid orphans. Unlike `balance`, works on longer text. Use for body copy.

```css
p {
  text-wrap: pretty;
}
```

### When to Use Which

| Scenario | Use |
| --- | --- |
| Headings, titles, short text (≤6 lines) | `text-wrap: balance` |
| Body paragraphs, descriptions | `text-wrap: pretty` |
| Code blocks, pre-formatted text | Neither |

## Font Smoothing (macOS)

Text renders heavier than intended by default on macOS. Apply antialiased smoothing to the root:

```css
html {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```

```tsx
<html className="antialiased">
```

Apply once at the root — not per-element. Other platforms ignore these properties, so it's safe to apply universally.

## Tabular Numbers

Use `font-variant-numeric: tabular-nums` for dynamically updating numbers to prevent layout shift.

```css
.counter {
  font-variant-numeric: tabular-nums;
}
```

```tsx
<span className="tabular-nums">{count}</span>
```

| Use tabular-nums | Don't use tabular-nums |
| --- | --- |
| Counters and timers | Static display numbers |
| Prices that update | Decorative large numbers |
| Table columns with numbers | Phone numbers, zip codes |
| Animated number transitions | Version numbers (v2.1.0) |
| Scoreboards, dashboards | |

Some fonts (like Inter) change numeral appearance with this property — the digit `1` becomes wider and centered. This is expected and usually desirable for alignment.
