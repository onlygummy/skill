---
name: pcf-fluent-integration
description: |
  Integrate Fluent UI React v9 (@fluentui/react-components) into PCF code components for Canvas Apps.
  Use when building a PCF control that needs Fluent UI components, theming inside a full-screen PCF,
  or ensuring Fluent UI usage stays standard-licensed. References pcf-canvas-standard and
  fluent-ui-react skills; does not duplicate their content.
license: MIT
metadata:
  author: onlygummy
  version: "1.0.0"
---

# PCF + Fluent UI Integration

This skill bridges `pcf-canvas-standard` (React PCF for Canvas Apps) and `fluent-ui-react`
(Fluent UI React v9). It covers **when and how** to combine them — not the full content of
either skill. Refer to those skills for setup details and component APIs.

## When to use this skill

- Building a PCF control that needs Fluent UI components (Button, Dialog, Input, etc.)
- Adding FluentProvider theming inside a full-screen PCF
- Ensuring Fluent UI usage in PCF stays standard-licensed
- Troubleshooting Fluent UI rendering issues inside Canvas Apps

## When to use both skills together

| Scenario | Use pcf-canvas-standard | Use fluent-ui-react | Use this skill |
|----------|------------------------|---------------------|----------------|
| Create PCF project, full-screen layout | ✅ | | |
| Standard license rules, manifest | ✅ | | |
| Multi-page navigation in PCF | ✅ | | |
| Fluent UI component APIs, props, styling | | ✅ | |
| Theming, tokens, FluentProvider | | ✅ | |
| Combining Fluent UI into PCF (this skill) | | | ✅ |
| Fluent UI + PCF licensing concerns | | | ✅ |
| FluentProvider setup inside PCF | | | ✅ |

**Rule of thumb**: Start with `pcf-canvas-standard` for project setup and licensing.
Add `fluent-ui-react` for component/styling knowledge. Use this skill for the
integration glue.

## Quick start: Fluent UI in a PCF project

See `references/setup.md` for full setup details.

```bash
# 1. Create PCF project (see pcf-canvas-standard)
pac pcf init --namespace MyApp --name MyAppControl --template field \
  --framework react --run-npm-install

# 2. Install Fluent UI
npm install @fluentui/react-components
```

```tsx
// MyAppControl.tsx
import * as React from 'react';
import { FluentProvider, webLightTheme, Button, makeStyles, tokens } from "@fluentui/react-components";
import { IInputs, IOutputs } from "./generated/ManifestTypes";

export class MyAppControl implements ComponentFramework.ReactControl<IInputs, IOutputs> {
  private context: ComponentFramework.Context<IInputs>;

  public init(context, notifyOutputChanged, state): void {
    this.context = context;
    context.mode.trackContainerResize(true);
  }

  public updateView(context): React.ReactElement {
    return (
      <FluentProvider theme={webLightTheme}>
        <div style={{ width: '100vw', height: '100vh', padding: '20px', boxSizing: 'border-box' }}>
          <Button appearance="primary">Hello from PCF</Button>
        </div>
      </FluentProvider>
    );
  }

  public getOutputs(): IOutputs { return {}; }
  public destroy(): void {}
}
```

## Theming inside PCF

FluentProvider must wrap the entire PCF React tree. See `fluent-ui-react` references/theming.md
for full theming details. See `references/theming.md` for PCF-specific theming notes.

```tsx
<FluentProvider theme={webLightTheme} dir="ltr">
  {/* full-screen layout */}
</FluentProvider>
```

- Use `webLightTheme` / `webDarkTheme` (or Teams themes).
- Nested FluentProvider can override specific tokens.
- `dir="rtl"` for right-to-left languages.
- FluentProvider renders a `div` — ensure it doesn't break full-screen layout (use 100vw/100vh
  on an inner container, not on FluentProvider itself).

## Licensing: Fluent UI is standard

Fluent UI (`@fluentui/react-components`) is a **standard** library — it does NOT make the PCF
control premium. No `<external-service-usage enabled="true">` needed.

### ✅ Safe (stays standard)

- Using Fluent UI components (Button, Dialog, Input, etc.)
- Fluent UI theming (tokens, FluentProvider)
- Griffel styling (makeStyles, mergeClasses)
- Fluent UI icons (`@fluentui/react-icons`) — bundled via npm

### ⚠️ Still forbidden (even with Fluent UI)

- Direct external API calls from React (`fetch`, `axios`)
- Runtime CDN loading (Google Fonts, external scripts)
- `context.webAPI` (Dataverse)
- `<external-service-usage enabled="true">`

### Golden rule (unchanged)

> React = Pure UI & Client Logic. Canvas App (Power Fx) = all external communication & data.

Fluent UI is just the UI layer — it doesn't change the licensing model.

## Bundle size considerations

Fluent UI adds to bundle size. Mitigations:

- Import only what you need: `import { Button } from "@fluentui/react-components"` (tree-shakeable).
- Use build-time styles (`@griffel/webpack-loader`) to optimize.
- Avoid importing entire `@fluentui/react-components` barrel if only using a few components.
- See `fluent-ui-react` references/build-performance.md for optimization details.

## Gotchas

- **FluentProvider must wrap everything**: Without it, tokens resolve to nothing and components
  render unstyled.
- **FluentProvider renders a div**: Don't set 100vw/100vh on FluentProvider itself — set on an
  inner container to avoid layout issues.
- **trackContainerResize + FluentProvider**: Both needed — trackContainerResize for PCF resize,
  FluentProvider for theming.
- **No SSR in PCF**: PCF controls are client-side only; don't use SSR patterns.
- **Icons**: Install `@fluentui/react-icons` via npm and bundle — don't load from CDN.
- **Full-screen + Fluent UI**: Ensure the inner container (not FluentProvider) uses 100vw/100vh.
- **Theming in Canvas App**: The Canvas App's theme doesn't automatically apply to Fluent UI —
  you must set the FluentProvider theme explicitly.

## Component selection guide

For common PCF use cases, prefer these Fluent UI components:

| Use case | Fluent UI component |
|----------|-------------------|
| Actions | Button, CompoundButton, SplitButton |
| Forms | Input, Textarea, Select, Checkbox, RadioGroup, Switch, SpinButton, Slider |
| Feedback | MessageBar, Toast, Spinner, ProgressBar |
| Overlays | Dialog, Popover, Tooltip, Drawer |
| Layout | Card, Divider, Toolbar, TabList, Accordion |
| Data display | Table, DataGrid, List, Tree |
| Navigation | Nav, Breadcrumb, TabList |
| Status | Badge, Avatar, Persona, PresenceBadge |

See `fluent-ui-react` references/component-index.md for full component list.

## Resources

- `pcf-canvas-standard` skill — PCF setup, licensing, navigation, API availability
- `fluent-ui-react` skill — Fluent UI components, styling, theming, slots, positioning
- Fluent UI docs: https://react.fluentui.dev/
- PCF overview: https://learn.microsoft.com/en-us/power-apps/developer/component-framework/overview
