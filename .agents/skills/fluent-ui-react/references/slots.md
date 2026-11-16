# Fluent UI React — Slots

## What slots are

Named parts of a component designed to be modified or replaced. Each slot is a top-level prop of
the same name. More flexible than render callbacks.

## When to use slots

- Set content of a component part
- Customize style of a part (via `className`)
- Customize props passed to a part
- Subscribe to event handlers of a part
- Change element type of a part (via `as`)
- Completely replace existing content

## When NOT to use slots

- Changing visual appearance of every instance → customize theme instead.
- Slightly adjusting display of one instance → apply custom style via `className`.
- Changing behavior / significant layout → use hooks API (building custom controls).

## Filling slots

### Shorthand value (primitive or JSX)

```tsx
<Button icon={<CalendarRegular />} />
<Input contentBefore="www." contentAfter=".com" />
```

Shorthand converts to the slot's children.

### Props object

Pass props of the rendered type:

```tsx
<Avatar badge={{ status: "available" }} />
```

### `className` for style

```tsx
<Avatar badge={{ className: styles.badge }} />
```

### `as` to change element type

```tsx
<Button icon={{ as: "img", src: "..." }} />
```

### Replace entire slot (render function)

Escape hatch — replaces containing element too. Verify accessibility/layout/styling.

```tsx
<Button icon={() => <b>B</b>} />
```

## Conditional rendering

Some slots only render conditionally (e.g. Avatar `label` only when no image; `icon` only when
no image/name).

## Children vs slots

Primary content = children. Slots are named parts around/within.

## For component developers

### Slot type

`Slot<Type, AlternateAs>` in `@fluentui/react-utilities`. `Type` = default element/component.
`AlternateAs` = allowed `as` values (intrinsic elements only).

### Slot definition in props

`ComponentProps<Slot>` / `ComponentState<Slot>`. Optional via `?`; non-nullable via
`NonNullable<T>` (slot must always render, e.g. RadioButton indicator).

### Special root slot

Every component has a `root` slot (top-level element). Props generally apply to root; `className`
and `style` always go to root. Primary slots receive forwarded props.

### Rendering with slots

- `useX_unstable` (props -> state) fills defaults, handlers, slot element types.
- `useXStyles_unstable` (state -> classNames).
- `renderX` renders; uses `assertSlots` + `createElement` (custom JSX pragma) for slot handling.
- `slot.always` / `slot.optional` define slots in state.

Suffix `_unstable` means possible future breaking change, not unfit for production.
