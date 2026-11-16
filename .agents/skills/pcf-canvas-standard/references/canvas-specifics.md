# PCF Canvas Standard — Canvas-Specific Limitations

## Feature toggle (admin required)

"Power Apps component framework for canvas apps" must be enabled per environment:

Power Platform admin center → Environments → select env → Settings → Product → Features →
turn on "Power Apps component framework for canvas apps" → Save.

Not enabled by default. Requires system admin + Power Apps license.

## Canvas component libraries

PCF controls do NOT work inside Canvas component libraries. Place directly on screens.

## Versioning

- Increment at least PATCH in `ControlManifest.Input.xml` version or changes won't be detected.
- Use `pac pcf version --strategy manifest` to bump.
- After deploy: reopen app in Studio → accept Update prompt → save & publish.
- Canvas app embeds a copy of the component → two apps can run different versions; one app can't
  run two versions.

## Managed Environments

- Managed environments require premium license for active usage (all app types).
- Feb 2027: stricter enforcement — users without qualifying license can't open apps.

## Power Pages

- Device APIs NOT supported.
- `uses-feature` must not be set to true.

## Security

- Studio shows warning about potentially unsafe code when app contains code components.
- "Default" publisher appears for controls imported via unmanaged solution or `pac pcf push`.
- Admins should review code components before making available.
- Components have access to security tokens/data while rendering in Studio (not when played).

## Deployment

- `pac pcf push` deploys into a named solution or temporary `PowerAppsTools_<prefix>`.
- Segmented solution recommended for shared components.
- Publisher prefix must match across environments.
- Code components not supported on-premises.
