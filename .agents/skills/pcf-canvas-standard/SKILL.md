---
name: pcf-canvas-standard
description: |
  Build React PCF (Power Apps Component Framework) code components for Canvas Apps with a standard
  license (Office 365): full-screen React UI, multi-page navigation, and client-side logic only.
  Use when creating a PCF control for Canvas Apps, setting up a full-screen React app inside Power
  Apps, implementing in-app navigation, or ensuring the component stays standard (no premium features).
license: MIT
metadata:
  author: onlygummy
  version: "1.0.0"
---

# PCF Canvas Standard (React, Full-Screen)

Build React-based Power Apps Component Framework (PCF) code components that run in Canvas Apps
under a **standard license** (Office 365 / Microsoft 365) — no premium license required.

Core principle: **React handles UI and client-side logic only; Canvas App (Power Fx) handles all
external data and services.**

## When to use this skill

- Creating a new PCF control for Canvas Apps with React
- Setting up a full-screen React app inside a Canvas App
- Implementing multi-page navigation inside a PCF control
- Ensuring a PCF component stays standard (no premium features)
- Reviewing a PCF manifest for licensing compliance

## Quick start

```bash
pac pcf init --namespace <Namespace> --name <ControlName> --template field \
  --framework react --run-npm-install
```

Open in VS Code, then build:

```bash
npm run build
pac pcf push --publisher-prefix <prefix>
```

## Full-screen React setup

### 1. Enable container resize tracking

In `init()`:

```ts
public init(context, notifyOutputChanged, state): void {
  this.theNotifyOutputChanged = notifyOutputChanged;
  this.context = context;
  context.mode.trackContainerResize(true); // re-render on resize
}
```

### 2. Use viewport units, not fixed pixels

```tsx
<div style={{
  width: '100vw',
  height: '100vh',
  boxSizing: 'border-box',
  padding: '20px',
  overflowY: 'auto',
}}>
  {/* your React UI */}
</div>
```

Never hardcode width/height in pixels. Use `100vw`/`100vh` or `%`.

### 3. Canvas App placement (critical)

After importing the solution into Power Apps Maker Portal:

1. Create a blank Canvas App (Tablet layout recommended for wider space).
2. Insert > Code > select your component onto Screen 1.
3. Set properties:
   - X: `0`
   - Y: `0`
   - Width: `Parent.Width`
   - Height: `Parent.Height`

The React PCF will auto-expand to full screen and stay responsive.

## Multi-page navigation

### Recommended: useState (SPA inside PCF)

Do NOT use `BrowserRouter` (react-router-dom) — it breaks the Power Apps URL. Use `MemoryRouter`
only if you need a real router.

```tsx
import * as React from 'react';
import { useState } from 'react';

export const MainApp: React.FC = () => {
  const [currentPage, setCurrentPage] = useState('dashboard');

  return (
    <div style={{ display: 'flex', width: '100vw', height: '100vh' }}>
      <nav style={{ width: '220px' }}>
        <button onClick={() => setCurrentPage('dashboard')}>Dashboard</button>
        <button onClick={() => setCurrentPage('stock-in')}>Stock In</button>
      </nav>
      <main style={{ flex: 1, overflowY: 'auto' }}>
        {currentPage === 'dashboard' && <DashboardPage />}
        {currentPage === 'stock-in' && <StockInPage />}
      </main>
    </div>
  );
};
```

### Alternative: Hybrid (Canvas Screens + PageName property)

1. Add `PageName` (SingleLine.Text, usage="input") to `ControlManifest.Input.xml`.
2. Read `context.parameters.PageName.raw` in React to switch views.
3. Place the same PCF on multiple Canvas Screens with different `PageName` values.
4. Use `Navigate(Screen2)` in Power Apps to switch screens.

Use hybrid when some screens need standard Power Apps controls mixed in.

## Standard license rules

### ✅ Allowed (no premium license needed)

- Full UI freedom: CSS, Tailwind, Fluent UI, complex components, animations
- Multi-page navigation inside the PCF (useState, MemoryRouter)
- Client-side processing: filter, search, sort, calculations, charts, QR/barcode generation
- Receiving data from Canvas App via Input properties (SharePoint, Excel, Office 365 Users)
- Sending values back via Output properties for `Patch()` or Power Automate (standard connectors)
- Device APIs via framework: `context.device.captureImage`, `captureBarcode`, `getCurrentPosition`,
  `pickFile` (must declare in `<feature-usage>`)

### ⚠️ Forbidden (makes the app Premium instantly)

- **Direct external API calls** from React (`fetch`, `axios`, `XMLHttpRequest` to SharePoint REST,
  Microsoft Graph, or any external API). Canvas App must fetch data and pass via Input properties.
- **Runtime CDN loading** (`<script src="https://...">`, Google Fonts via URL, external images/icons).
  Install everything via `npm install` and bundle into the solution.
- **`<external-service-usage enabled="true">`** in the manifest.
- **`context.webAPI`** — designed for Dataverse (Premium). Never use in Canvas Apps.
- **Model-driven apps or Managed Environments** — Canvas App only, standard environment.

### Golden rule

> React = Pure UI & Client Logic. Canvas App (Power Fx) = all external communication & data.

## Manifest rules

### Required for standard (no premium)

```xml
<external-service-usage enabled="false" />
```

### Device features (declare if used)

```xml
<feature-usage>
  <uses-feature name="Device.captureImage" required="true" />
  <uses-feature name="Device.getBarcodeValue" required="true" />
  <uses-feature name="Device.getCurrentPosition" required="true" />
  <uses-feature name="Device.pickFile" required="true" />
</feature-usage>
```

Available device features: `Device.captureAudio`, `Device.captureImage`, `Device.captureVideo`,
`Device.getBarcodeValue`, `Device.getCurrentPosition`, `Device.pickFile`.

### Input property example

```xml
<property name="PageName" display-name-key="PageName" description-key="PageName_Desc"
  of-type="SingleLine.Text" usage="input" required="false" />
```

See `assets/manifest-template.xml` for a full standard manifest.

## Gotchas

- **Feature toggle**: Admin must enable "Power Apps component framework for canvas apps" in
  Environment settings (Settings > Product > Features) before use. Not enabled by default.
- **Canvas component libraries**: PCF controls do NOT work inside Canvas component libraries.
- **Versioning**: Increment at least PATCH in manifest version or changes won't be detected.
  Reopen app in Studio, accept Update prompt, save & publish.
- **Canvas app embeds a copy**: Two canvas apps in same environment can run different versions;
  one app can't run two versions.
- **Managed Environment** (Feb 2027): stricter licensing — active usage requires premium license
  for all app types.
- **Power Pages**: Device APIs NOT supported.
- **`context.webAPI`**: runtime error in Canvas Apps ("Method not implemented").
- **Xrm namespace**: not available in Canvas Apps.

## API availability (Canvas vs Model-driven)

| API | Canvas | Model-driven |
|-----|--------|--------------|
| `context.device.*` | ✅ | ✅ |
| `context.webAPI` | ❌ | ✅ |
| `context.mode.trackContainerResize` | ✅ | ✅ |
| `context.formatting` | ✅ | ✅ |
| `context.parameters` | ✅ | ✅ |
| `context.navigation` | ✅ | ✅ |
| `context.resources` | ✅ | ✅ |

See `references/api-availability.md` for full details.

## Resources

- PCF overview: https://learn.microsoft.com/en-us/power-apps/developer/component-framework/overview
- Manifest schema: https://learn.microsoft.com/en-us/power-apps/developer/component-framework/manifest-schema-reference/external-service-usage
- Device API: https://learn.microsoft.com/en-us/power-apps/developer/component-framework/reference/device
- Licensing FAQ: https://learn.microsoft.com/en-us/power-platform/admin/powerapps-licensing-faq
