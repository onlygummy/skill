# PCF Canvas Standard — Licensing

## Standard vs Premium

PCF components are classified by how they connect to data:

| Type | Behavior | License needed |
|------|----------|----------------|
| **Standard** | No external service/data connection via browser | Office 365 (standard) |
| **Premium** | Connects to external services/data directly via browser | Power Apps Premium |

A component's classification is declared in the manifest via `<external-service-usage>`.

## Standard license rules

### ✅ Allowed (stays standard)

- Pure UI rendering and client-side logic
- CSS/styling (Tailwind, Fluent UI, custom)
- Filter, search, sort, calculations in browser
- Charts, QR code, barcode generation
- Device APIs via `context.device.*` (declared in feature-usage)
- Receiving data via Input properties from Canvas App
- Sending values via Output properties to Canvas App
- Calling Power Automate flows with standard connectors (via Canvas App)

### ⚠️ Makes app Premium instantly

- Direct external API calls from React (`fetch`, `axios`, `XMLHttpRequest`)
- Loading external CDN resources at runtime (`<script src="https://...">`, Google Fonts URLs)
- `<external-service-usage enabled="true">` in manifest
- Using `context.webAPI` (Dataverse = Premium)
- Running in Model-driven apps or Managed Environments
- Mixing Dataverse/SQL tables in the same Canvas App

## Manifest: external-service-usage

```xml
<!-- Standard (no premium) -->
<external-service-usage enabled="false" />

<!-- Premium (external service) -->
<external-service-usage enabled="true">
  <domain>www.contoso.com</domain>
</external-service-usage>
```

## Managed Environments (Feb 2027)

Starting February 2027, managed environments will enforce stricter licensing. Active usage
requires a qualifying premium license for all app types, including standard apps.

## License designation check

In Power Apps Maker Portal: Apps → select app → Settings/Details → "License designation".
Shows Standard, Extended, or Premium.

## Golden rule

> React = Pure UI & Client Logic. Canvas App (Power Fx) = all external communication & data.
