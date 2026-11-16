# Fluent UI React — Styling

Deep-dive reference for styling with Griffel.

## makeStyles

```tsx
import { makeStyles, mergeClasses, tokens } from "@fluentui/react-components";

const useStyles = makeStyles({
  root: { display: "flex", gap: tokens.spacingHorizontalM },
  primary: { backgroundColor: tokens.colorBrandBackground },
});

function MyComp({ className }) {
  const styles = useStyles();
  return <div className={mergeClasses(styles.root, className)} />;
}
```

- Call `makeStyles` at module scope; returns a hook.
- Each key maps to an atomic class; values are style objects.
- Griffel resolves `tokens.*` to CSS variables.

## mergeClasses

Always use `mergeClasses()` to combine classes — never string concatenation, because atomic
classes need deduplication and proper override ordering (later wins, like `Object.assign`).

```tsx
mergeClasses(styles.root, styles.primary, className)
```

## Mapping props/state to styles

`makeStyles`/`mergeClasses` are unaware of state. Manually apply parts based on props:

```tsx
const classes = mergeClasses(
  styles.root,
  disabled && styles.disabled,
  appearance === "primary" && styles.primary,
  className
);
```

## Applying parent className

Pass parent classes last so they win:

```tsx
<div className={mergeClasses(styles.root, className)} />
```

## Theme tokens in styles

Use `tokens.*` for all theme values. Tokens resolve to CSS variables set by FluentProvider.
Never use CSS variables directly — names may change.

## Griffel limitations

- No CSS shorthand properties. Use Griffel shorthand functions
  (https://griffel.js.org/react/api/shorthands).
- Styles are atomic; ordering handled by Griffel.

## Overriding component styles

Same approach — call `makeStyles`/`useStyles` and pass classes via props (e.g. `className`).

## Advanced styling techniques

### Custom style hooks

Components use `useX_unstable` (state) + `useXStyles_unstable` (styles). App-wide override via
`CustomStyleHooksProvider_unstable`:

```tsx
import { CustomStyleHooksProvider_unstable } from "@fluentui/react-components";
// provide custom useButtonStyles_unstable etc.
```

Custom hooks must append the slot's original `className` prop (via
`getSlotClassNameProp_unstable`) after custom styles so user `className` wins.

### Media queries sorting

Pass `compareMediaQueries` to the Griffel renderer for deterministic ordering. For mobile-first,
use `sort-css-media-queries`.

### CSP

Use `styleElementAttributes` to add `nonce` for Content Security Policies.

### Child window / iframe rendering

Provide `targetDocument` to `FluentProvider` and `RendererProvider`, plus `createDOMRenderer`.

### Build-time optimization

Use `@griffel/webpack-loader` or the Babel preset to pre-compute styles (optimizes first render).
See `references/build-performance.md`.

### Debugging

Griffel DevTools Chrome extension shows applied styles and overrides.

## Anti-patterns

- Concatenating class strings.
- Using CSS variables directly.
- Calling `makeStyles` inside a component (must be module scope).
- Hardcoding theme values instead of tokens.
