# Gestures

Drag interactions, momentum, damping, pointer capture, and friction.

## Momentum-Based Dismissal

Don't require dragging past a threshold. Calculate velocity — a quick flick should be enough:

```js
const timeTaken = new Date().getTime() - dragStartTime.current.getTime();
const velocity = Math.abs(swipeAmount) / timeTaken;

if (Math.abs(swipeAmount) >= SWIPE_THRESHOLD || velocity > 0.11) {
  dismiss();
}
```

If velocity exceeds ~0.11, dismiss regardless of distance.

## Damping at Boundaries

When a user drags past the natural boundary (e.g., dragging a drawer up when already at top), apply damping. The more they drag, the less the element moves. Things in real life don't suddenly stop — they slow down first.

## Pointer Capture

Once dragging starts, set the element to capture all pointer events. This ensures dragging continues even if the pointer leaves the element bounds.

```js
element.setPointerCapture(event.pointerId);
```

## Multi-Touch Protection

Ignore additional touch points after the initial drag begins. Without this, switching fingers mid-drag causes the element to jump:

```js
function onPress(event) {
  if (isDragging) return;
  // Start drag...
}
```

## Friction Instead of Hard Stops

Instead of preventing upward drag entirely, allow it with increasing friction. It feels more natural than hitting an invisible wall.

Common implementation: divide the drag distance by an increasing factor as it exceeds the boundary.

```js
const overshoot = currentPosition - boundary;
const dampedPosition = boundary + overshoot * (1 / (1 + overshoot * 0.01));
```

## Spring-Based Drag

Use spring animations for drag release — they maintain velocity when interrupted, unlike CSS transitions which restart from zero.

```jsx
const springX = useSpring(0, { stiffness: 300, damping: 30 });

function onDragEnd(_, info) {
  if (Math.abs(info.velocity.x) > 0.11) {
    // Dismiss with momentum
    springX.set(info.velocity.x > 0 ? 500 : -500);
  } else {
    // Snap back
    springX.set(0);
  }
}
```
