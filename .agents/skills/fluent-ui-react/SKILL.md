---
name: fluent-ui-react
description: |
  Build UIs with Fluent UI React v9 (@fluentui/react-components): components, styling (Griffel/makeStyles),
  theming (tokens, FluentProvider), slots, positioning, accessibility, SSR, motion, and migration from v8/v0.
  Use when creating or styling React components with Fluent UI, choosing components, configuring themes,
  handling positioning, accessibility, SSR, or migrating from v8/v0 to v9.
license: MIT
metadata:
  author: onlygummy
  version: "1.0.0"
  source: "https://storybooks.fluentui.dev/react"
---

# Fluent UI React (v9)

Fluent UI React v9 is a set of React components implementing Microsoft's Fluent Design System.
Package: `@fluentui/react-components` (suite). Current docs version ~v9.75.

## Quick start

```bash
npm install @fluentui/react-components
```

```tsx
import { FluentProvider, webLightTheme, Button } from "@fluentui/react-components";

export default function App() {
  return (
    <FluentProvider theme={webLightTheme}>
      <Button appearance="primary">Hello</Button>
    </FluentProvider>
  );
}
```

Place `<FluentProvider theme={...} />` at the root. It renders a `div`, sets theme tokens as CSS
variables, and propagates them to portals. Supports `dir="ltr"|"rtl"`.

## Core architecture

- **Styling**: CSS-in-JS via Griffel (`makeStyles`, `mergeClasses`). Styles are atomic classes.
- **Theming**: flat token object -> CSS variables. Components reference `tokens.*`. Never use CSS
  variables directly.
- **Slots**: components expose named slots (root, icon, contentBefore, ...). Fill with primitives,
  JSX, props objects, or render functions. Use `as` to change element type.
- **Hooks**: `useX_unstable` (state) + `useXStyles_unstable` (styles) + `renderX` (render). Base
  state hooks (`useXBase_unstable`) provide behavior without styling for custom controls.
- **Positioning**: components with positioned slots accept a `positioning` prop (shorthand or object).

## Styling components

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

Rules:
- Call `makeStyles` at module scope; it returns a hook.
- Use `mergeClasses()` to combine classes (never string-concatenate — atomic classes need dedup).
- Map props/state to styles manually (makeStyles is unaware of state).
- Apply parent `className` via `mergeClasses(componentClass, className)`.
- Reference theme via `tokens.*` (resolved to CSS variables). Never hardcode CSS variables.
- Griffel limitations: no CSS shorthand properties — use Griffel shorthand functions.

### Overriding component styles

Same approach: call `makeStyles`/`useStyles` in your code and pass classes through props. For
app-wide customization use `CustomStyleHooksProvider_unstable` with custom style hooks.

### Advanced styling

- Custom style hooks: `useXStyles_unstable` + `CustomStyleHooksProvider_unstable`.
- Media queries sorting: pass `compareMediaQueries` to Griffel renderer.
- CSP: use `styleElementAttributes` for `nonce`.
- Child window / iframe rendering: provide `targetDocument` + `RendererProvider`.

## Theming

Themes are flat `{ [token]: CSS value }` objects. FluentProvider sets tokens as CSS variables.

Built-in themes: `webLightTheme`, `webDarkTheme`, `teamsLightTheme`, `teamsDarkTheme`,
`teamsHighContrastTheme` (legacy — prefer OS high contrast).

Custom brand ramp: `createLightTheme()`, `createDarkTheme()`, `createTeamsDarkTheme()`,
`createHighContrastTheme()`.

Override tokens by copying the theme object and overwriting keys. Extend with new tokens
(beware: each token adds CSS variables to every DOM node).

Do NOT use High Contrast themes for new apps — components support Windows High Contrast mode
automatically.

See `references/theming.md`.

## Slots

- Each slot is a top-level prop of the same name.
- Fill with: primitive value, JSX, props object, or render function.
- `className` customizes slot style; `as` changes element type.
- Replacing the entire slot: pass a render function (escape hatch).
- Optional slots can be set to `null` to opt out.
- `root` slot is always present; primary slots receive forwarded props.

See `references/slots.md`.

## Positioning

Components with positioned slots accept a `positioning` prop (shorthand string like
`"above-start"` or full object). Key options: `position`, `align`, `offset`, `flipBoundary`,
`overflowBoundary`, `autoSize`, `coverTarget`, `shiftToCoverTarget`, `pinned`, `matchTargetSize`,
`strategy`, `useTransform`, `fallbackPositions`, `positioningRef`, `onPositioningEnd`.

See `references/positioning.md`.

## Accessibility

Components aim for WCAG 2.1 + WAI-ARIA compliance: semantic DOM, ARIA attributes, keyboard
navigation, screen reader support, touch, focus handling, color contrast, themes, focus indicator,
zoom up to 400%. Uses `tabster` for focus management.

Out of scope: i18n, globalization, keyboard shortcuts, app-level focus handling.

Best practices: label components, use `aria-describedby`/`aria-label` on DialogSurface, never
ship a Dialog without a focusable element, set `aria-modal="false"` when embedding popups inside
dialogs.

See `references/accessibility.md`.

## Server-Side Rendering

Supported. Provide `RendererProvider`, `SSRProvider`, and `FluentProvider` at root. Dialog:
set `unmountOnClose={true}` (default) for SSR to avoid portal hydration errors.

## Package maturity

- **Stable** (`9.x.x`): production-ready, `@fluentui/react-components` suite, no breaking changes.
- **Preview** (`0.x.x`): early access, `-preview` suffix, possible breaking changes.
- **Compat** (`0.x.x`): v8 components adapted to v9, `-compat` suffix, migration bridge.
- **Deprecated**: unsupported, migrate to stable alternative.

## Motion

Motion APIs: `createMotionComponent`, `createPresenceComponent`, `motionSlot`,
`presenceMotionSlot`, `PresenceGroup`. Motion components (preview): Atoms, Blur, Collapse, Fade,
Rotate, Scale, Slide, Stagger. Use motion slots on components. See `references/motion.md`.

## Utilities

- **Focus management**: `useArrowNavigationGroup`, `useFocusableGroup`, `useFocusFinders`,
  `useModalAttributes`, `useObservedElement`, `useRestoreFocusSource/Target`,
  `useUncontrolledFocus`.
- **ARIA live**: `AriaLiveAnnouncer`, `useAnnounce`, `useTypingAnnounce`.
- **Positioning**: `useSafeZoneArea`.
- **Theme**: `createCSSRuleFromTheme`.

## Component catalog

### Stable components (in `@fluentui/react-components`)

Accordion, Avatar, AvatarGroup, Badge (Badge/CounterBadge/PresenceBadge), Breadcrumb, Button
(Button/CompoundButton/MenuButton/SplitButton/ToggleButton), Card (Card/CardFooter/CardHeader/
CardPreview), Carousel (Carousel/CarouselNav), Checkbox, ColorPicker, Combobox, DataGrid, Dialog,
Divider, Drawer, Dropdown, Field, FluentProvider, Image, InfoLabel, Input, Label, Link, List,
Menu (Menu/MenuList), MessageBar, Nav, Overflow, Persona, Popover, Portal (Portal/toMountNodeProps),
ProgressBar, RadioGroup, Rating, RatingDisplay, SearchBox, Select, Skeleton, Slider, SpinButton,
Spinner, SwatchPicker, Switch, Table, TabList, Tag (Tag/TagGroup/InteractionTag), TagPicker,
TeachingPopover, Text, Textarea, Toast, Toolbar, Tooltip, Tree.

### Compat (v8 bridge): Calendar, DatePicker, TimePicker.

### Preview: Calendar, MenuGrid, RangeSlider (plus various preview packages).

### Motion (preview): Atoms, Blur, Collapse, Fade, Rotate, Scale, Slide, Stagger, Stagger choreography.

### Migration shims: v0 shims (Attachment, ButtonMixins, FlexShim, FormFieldShim, GridShim,
InputMixins, List, SegmentShim, SliderMixin, SpinnerMixins, Video) and v8 button shims
(ActionButton/CommandButton/CompoundButton/DefaultButton/MenuButton/PrimaryButton/ToggleButton),
CheckboxShim, StackShim, Theme shims (createBrandVariants/createV8Theme/createV9Theme).

## Migration from v8 / v0

Key changes: build-time styles (Griffel), design tokens (not component-specific props), declarative
children (TSX instead of data arrays), slots (replace render callbacks). Incremental adoption
supported — run v9 alongside v8/v0.

See `references/migration.md` for migration stages, challenges, and first-component guide.

## Build & performance

- **Build-time styles**: use `@griffel/webpack-loader` or babel preset to pre-compute styles.
  Optimizes first render.
- **Unprocessed (raw) styles**: `.raw.js` modules for multi-bundle apps to avoid CSS clashes
  (v9.67.0+).
- **Native ESM**: packages are `"type": "module"`, dual build (ESM in `lib/`, CJS in
  `lib-commonjs/`).

## Browser & platform support

- Browsers: Edge/Firefox/Chrome/Safari full support (Edge>=84, FF>=75, Chrome>=84, Safari>=14.1);
  partial support lower. IE not supported.
- React: 17 (v9.0.0+), 18 (v9.66.0+), 19 (v9.72.2+).
- TypeScript: 3 latest major versions.

## Best practices / gotchas

- Always wrap app in `FluentProvider` with a theme.
- Use `mergeClasses`, never string concatenation.
- Reference `tokens.*`, never CSS variables directly.
- Keep `makeStyles` at module scope.
- Slots: prefer props/JSX over render-function replacement.
- Dialog needs at least one focusable element; avoid nested dialogs.
- `disabledFocusable` for tab-order preservation in menus/commandbars.
- WCAG target size: buttons >= 24x24 (default covers this).
- Sentence-case button labels; single verb + noun.
- For high contrast, rely on OS mode (don't use legacy high-contrast themes).
- Migration: start with Button; use shims for incremental migration.

## Gotchas

- FluentProvider must wrap the app; without it, tokens resolve to nothing and components
  render unstyled.
- `makeStyles` must be called at module scope — calling inside a component recreates styles
  every render.
- Never string-concatenate classes; use `mergeClasses` (atomic classes need dedup).
- Reference `tokens.*`, never CSS variables directly (names may change).
- Griffel has no CSS shorthand properties — use Griffel shorthand functions.
- Dialog needs at least one focusable element; avoid nested dialogs.
- Popups inside a Dialog need `aria-modal={false}` on DialogSurface for VoiceOver.
- `disabledFocusable` (not `disabled`) preserves tab order in menus/commandbars.
- High-contrast themes are legacy; rely on OS high-contrast mode.
- v9.67.0+ raw styles (`.raw.js`) needed for multi-bundle apps to avoid CSS clashes.
- React 18 strict mode has known bugs; disable strict mode in Next.js for SSR hydration.
- Dialog SSR: keep `unmountOnClose={true}` (default) to avoid portal hydration errors.

## Resources

- Docs: https://react.fluentui.dev/
- Storybook (llms.txt): https://storybooks.fluentui.dev/react/llms.txt
- GitHub: https://github.com/microsoft/fluentui
- Griffel: https://griffel.js.org
- Styling handbook: https://github.com/microsoft/fluentui/blob/master/docs/react-v9/contributing/rfcs/react-components/styles-handbook.md
- Base state hooks RFC: https://github.com/microsoft/fluentui/blob/master/docs/react-v9/contributing/rfcs/react-components/convergence/base-state-hooks.md
