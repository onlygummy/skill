# PCF + Fluent UI — Setup

Integration-specific setup for using Fluent UI in a PCF project.

## Install

```bash
npm install @fluentui/react-components
```

Optional (icons, utilities):

```bash
npm install @fluentui/react-icons
```

## FluentProvider in PCF

FluentProvider must wrap the entire app. Place it in `updateView()`:

```tsx
import { FluentProvider, webLightTheme } from '@fluentui/react-components';

public updateView(context): React.ReactElement {
  return (
    <FluentProvider theme={webLightTheme}>
      <div style={{ width: '100vw', height: '100vh', padding: '20px' }}>
        {/* Fluent UI components here */}
      </div>
    </FluentProvider>
  );
}
```

## Full-screen + FluentProvider

Combine full-screen layout (from @pcf-canvas-standard) with FluentProvider:

```tsx
<FluentProvider theme={webLightTheme}>
  <div style={{ width: '100vw', height: '100vh', boxSizing: 'border-box', padding: '20px', overflowY: 'auto' }}>
    <Button appearance="primary">Hello</Button>
  </div>
</FluentProvider>
```

## Bundling

- All Fluent UI code is bundled into the solution via webpack (npm install).
- No external CDN at runtime (standard license rule).
- Tree-shaking works with webpack for production builds.

## Cross-references

- Setup, full-screen, navigation: see @pcf-canvas-standard
- Styling, theming, slots, components: see @fluent-ui-react
