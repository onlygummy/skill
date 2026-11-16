# Fluent UI React — Motion

## Introduction

Motion provides animation primitives built on Web Animations API.

## APIs

- `createMotionComponent()` — factory for custom motion components.
- `createMotionComponentVariant()` — variant based on existing motion component.
- `createPresenceComponent()` — React component animating presence via `visible` prop.
- `motionSlot()` / `presenceMotionSlot()` — for component authors integrating motion into slots.
- `PresenceGroup` — manages set of presence components in a list (state machine for mount/unmount).

## Motion components (preview)

Atoms, Blur, Collapse, Fade, Rotate, Scale, Slide, Stagger (choreography).

- `Fade` — fade in/out presence.
- `Collapse` — height/width expand/collapse.
- `Scale` — scale in/out.
- `Slide` — slide in/out.
- `Blur` — blur in/out.
- `Rotate` — 3D rotation (X/Y/Z).
- `Stagger` — choreography for staggered children.

## Using motion slots (consumers)

Components expose motion slots. Configure via props or replace via render function.

## Motion tokens

`motionTokens` provides easing curves and durations.

## Migration

Motion APIs may differ from v8. See `references/migration.md` for general migration guidance.

## Custom motion on Dialog

`Dialog`'s `surfaceMotion` accepts `Scale` params (duration, outScale, easing, animateOpacity).
`DialogSurface`'s `backdropMotion` accepts `Fade` params. Both support render function for full
custom implementation.
