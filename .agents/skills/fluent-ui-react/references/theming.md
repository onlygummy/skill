# Fluent UI React — Theming

## How theme is applied

Components reference theme tokens (`tokens.*`) which resolve to CSS variables. FluentProvider sets
those variables on its root `div` and changes them when the theme changes. Component styles stay
the same; only variables change.

Place `<FluentProvider theme={...} />` at app root.

## Do not use CSS variables directly

CSS variable names are internal and may change. Always use `tokens`.

## Available themes

- `webLightTheme`, `webDarkTheme`
- `teamsLightTheme`, `teamsDarkTheme`
- `teamsHighContrastTheme` (legacy — avoid for new apps)

Components support Windows High Contrast mode automatically regardless of active theme.

## Custom brand ramp

```tsx
import { createLightTheme, createDarkTheme } from "@fluentui/react-components";

const myTheme = createLightTheme(myBrandRamp);
```

Factory functions: `createLightTheme`, `createDarkTheme`, `createTeamsDarkTheme`,
`createHighContrastTheme`.

## Overriding existing tokens

Copy the theme object and overwrite keys:

```tsx
const custom = {
  ...webLightTheme,
  colorBrandBackground: "#ff0000",
};
```

Theme serialization escapes `<`, `>`, `{`, `}`, `;`, `\` in token names/values (containment, not
validation). Validate dynamic theme data against an app schema.

## Extending theme with new tokens

Add keys to the theme object. Warning: each token adds a CSS variable to every DOM node — impacts
runtime performance.

## Nesting providers

```tsx
<FluentProvider theme={webLightTheme}>
  <FluentProvider theme={{ colorBrandBackground2: "#fa8072" }}>
    ...
  </FluentProvider>
</FluentProvider>
```

Nested providers override only specified tokens.

## Dir (RTL)

```tsx
<FluentProvider dir="rtl" lang="ar">...</FluentProvider>
```

## Tokens reference

Browse all tokens in the Theme section of docs: Colors, Fonts, Spacing, Typography, Shadows,
Border Radii, Stroke Widths. Key token groups: `colorNeutral*`, `colorBrand*`, `colorStatus*`,
`spacing*`, `font*`, `borderRadius*`, `shadow*`, `strokeWidth*`.
