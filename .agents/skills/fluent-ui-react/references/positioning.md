# Fluent UI React — Positioning

Components with positioned slots accept a `positioning` prop (shorthand string or object).

## Shorthand

```tsx
<Popover positioning="above-start" />
```

## Full object

```tsx
<Popover positioning={{ position: "above", align: "start", offset: { mainAxis: 10, crossAxis: 10 } }} />
```

## Key options

| Option | Type | Description |
|--------|------|-------------|
| `position` | "before"\|"after"\|"below"\|"above" | Position (higher priority than align) |
| `align` | "center"\|"start"\|"end"\|"bottom"\|"top" | Alignment (only with position) |
| `pinned` | boolean | Disable auto-repositioning |
| `arrowPadding` | number | Padding between corner and arrow |
| `autoSize` | boolean\|"height"\|"width" | Fit within viewport (max height/width) |
| `coverTarget` | boolean | Position to cover target |
| `shiftToCoverTarget` | boolean | Shift to cover when no space |
| `fallbackPositions` | array | Fallback placements if flip fails |
| `flipBoundary` | element\|null | Boundary for flip behavior |
| `offset` | `{mainAxis,crossAxis}` or function | Displace from target |
| `overflowBoundary` | element\|null\|Rect | Boundary for overflow |
| `overflowBoundaryPadding` | number or per-side | Padding for early overflow detection |
| `strategy` | "fixed"\|"absolute" | CSS position type (default absolute) |
| `useTransform` | boolean (default true) | Use CSS transform for positioning |
| `matchTargetSize` | "width" | Match dimension(s) of target |
| `target` | element\|null | Manual target override |
| `positioningRef` | ref | Imperative handle (setTarget, updatePosition) |
| `onPositioningEnd` | callback | Fired when position update finishes (detail: placement, escaped, referenceHidden) |
| `disableUpdateOnResize` | boolean | Disable resize observer |

## Best practices

- `position` higher priority than `align`; if both vertical/horizontal conflict, align ignored and
  center used.
- `autoSize` sets inline max-width/max-height to fit viewport.
- `coverTarget` offsets to cover target.
- `matchTargetSize: "width"` makes positioned element match target width (use `box-sizing: border-box`).
- Imperative `updatePosition()` for dynamic target movement.
- `onPositioningEnd` provides `placement`, `escaped`, `referenceHidden` for visibility responses.

## Components using positioning

Tooltip, Menu, Popover, and any component with positioned slots. All share the same `positioning` prop.
