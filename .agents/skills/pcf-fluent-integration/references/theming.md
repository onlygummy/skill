# PCF + Fluent UI — Theming

Integration-specific theming considerations for Fluent UI inside PCF.

## Themes available

- `webLightTheme`, `webDarkTheme`
- `teamsLightTheme`, `teamsDarkTheme`
- `teamsHighContrastTheme` (legacy — avoid for new apps)

## Default theme in PCF

Use `webLightTheme` as default. For dark mode, use `webDarkTheme` or detect from Canvas App.

```tsx
import { FluentProvider, webLightTheme, webDarkTheme } from '@fluentui/react-components';

<FluentProvider theme={isDark ? webDarkTheme : webLightTheme}>
  ...
</FluentProvider>
```

## Nested providers

Override specific tokens for a section:

```tsx
<FluentProvider theme={webLightTheme}>
  <div>
    Normal section
    <FluentProvider theme={{ colorBrandBackground: '#ff0000' }}>
      <div>Overridden section</div>
    </FluentProvider>
  </div>
</FluentProvider>
```

## RTL support

```tsx
<FluentProvider theme={webLightTheme} dir="rtl" lang="ar">
  ...
</FluentProvider>
```

## Canvas App context

- Canvas App may have its own theme (light/dark). Consider passing a theme prop from Canvas App
  to the PCF via Input property.
- Fluent UI theming is independent of Canvas App theming — they don't auto-sync.
- Use `tokens.*` in makeStyles, never CSS variables directly.

## Do not use CSS variables directly

CSS variable names are internal and may change. Always use `tokens`.

## High contrast

Components support Windows High Contrast mode automatically. Don't use legacy high-contrast themes.

For full theming details, see @fluent-ui-react (references/theming.md).
