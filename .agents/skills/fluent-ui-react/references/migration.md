# Fluent UI React — Migration (v8/v0 → v9)

## Major changes

### Build-time styles
v8: runtime styles via theme on context. v9: build-time atomic CSS (Griffel). Improves render
perf and bundle size.

### Design tokens
v8: component-specific props (inputTextHovered). v9: design tokens (colorNeutralForeground2Hover).
Fewer, reusable tokens; no new tokens per component.

### TSX/JSX children
v8: data arrays/objects + render callbacks. v9: declarative children (TSX). Idiomatic React,
caller controls memoization/lazy loading.

### Slots
v8: render callback props. v9: slots (literals, JSX, or render functions). Consistent extensibility.

## Expected challenges

- Design differences (Fluent 1 vs Fluent 2). Use Keeping Design Consistent techniques.
- Custom styles: no custom styles prop / runtime theme / SASS. Prefer defaults.
- Props changes: update code per component migration docs.
- Build updates: tree-shaking, CSS bundling, browser matrix.
- Temporary bundle size increase during migration (both versions). v9 components smaller
  ultimately.

## Migration stages

1. Get familiar with v9 (docs, try components, inspect HTML/CSS, roadmap).
2. Assess codebase (usage count, custom styling, data binding, abstractions).
3. Decide milestones (flight, horizontal vs vertical migration).
4. Migrate some experiences (start with Button; record effort).
5. Build new experiences in v9.
6. Incrementally migrate core experiences.
7. Migrate remaining long tail (use shims / contribute).

## Migrating first component (Button example)

1. Locate usages (ActionButton, PrimaryButton, SplitButton).
2. Add `@fluentui/react-components`.
3. Update imports.
4. Update props (`<PrimaryButton>` -> `<Button appearance="primary" />`).
5. Add `FluentProvider` with theme where `ThemeProvider` used.
6. Build and test.

## Keeping design consistent

Techniques/tools to make v9 look like v8 or vice versa during migration.

## Handling breaking changes

See component-specific migration docs. Shims available for incremental migration.
