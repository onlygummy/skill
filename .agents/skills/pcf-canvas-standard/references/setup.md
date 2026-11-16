# PCF Canvas Standard — Setup

Detailed setup guide for creating a React PCF control for Canvas Apps.

## Prerequisites

- Power Platform CLI (`pac`) installed
- Node.js + npm
- VS Code
- Power Apps environment with "Power Apps component framework for canvas apps" enabled (admin)

## Project creation

```bash
pac pcf init --namespace <Namespace> --name <ControlName> --template field \
  --framework react --run-npm-install
```

Templates: `field` (single value) or `dataset` (grid/table). Use `field` for full-screen apps.

## Project structure

```
<ControlName>/
├── ControlManifest.Input.xml   ← manifest (licensing, features, properties)
├── index.ts                    ← entry point (init, updateView, getOutputs, destroy)
├── <ControlName>.tsx           ← React component
├── generated/ManifestTypes.d.ts ← auto-generated types
└── package.json
```

## Full-screen React code

```tsx
import * as React from 'react';
import { IInputs, IOutputs } from "./generated/ManifestTypes";

export class MyControl implements ComponentFramework.ReactControl<IInputs, IOutputs> {
  private theNotifyOutputChanged: () => void;
  private context: ComponentFramework.Context<IInputs>;

  public init(context, notifyOutputChanged, state): void {
    this.theNotifyOutputChanged = notifyOutputChanged;
    this.context = context;
    context.mode.trackContainerResize(true);
  }

  public updateView(context): React.ReactElement {
    return (
      <div style={{
        width: '100vw',
        height: '100vh',
        boxSizing: 'border-box',
        backgroundColor: '#f3f2f1',
        padding: '20px',
        overflowY: 'auto',
      }}>
        <h1>My Full-Screen App</h1>
      </div>
    );
  }

  public getOutputs(): IOutputs { return {}; }
  public destroy(): void {}
}
```

## CSS rules

- Use `100vw`/`100vh` or `%` — never fixed pixels for width/height.
- Use `boxSizing: 'border-box'`.
- Add `overflowY: 'auto'` for scrollable content.
- For responsive layouts, use flexbox/grid with `flex: 1`.

## Build & deploy

```bash
npm run build
pac pcf push --publisher-prefix <prefix>
```

`pac pcf push` deploys into a named solution or a temporary `PowerAppsTools_<prefix>` solution.

## Canvas App placement

1. Create blank Canvas App (Tablet layout recommended).
2. Insert > Code > select your component.
3. Set: X=0, Y=0, Width=Parent.Width, Height=Parent.Height.

## Environment feature toggle

Admin must enable in Power Platform admin center:
Environments → select env → Settings → Product → Features → "Power Apps component framework for canvas apps" → Save.

## Versioning

- Increment at least PATCH in manifest version or changes won't be detected.
- Use `pac pcf version --strategy manifest` to bump.
- After deploy: reopen app in Studio → accept Update prompt → save & publish.

## Common pitfalls

- Hardcoding width/height in pixels → not responsive.
- Forgetting `trackContainerResize(true)` → no re-render on resize.
- Not enabling feature toggle → component won't appear in Studio.
- Not incrementing version → app keeps old version.
- Using `BrowserRouter` → breaks Power Apps URL.
