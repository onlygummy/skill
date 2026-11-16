# Fluent UI React — Build & Performance

## Build-time styles

Fluent uses Griffel CSS-in-JS. Runtime cost only on first render. Optimize at build time.

### Webpack loader

```js
// webpack.config.js
module.exports = {
  module: {
    rules: [
      { test: /\.tsx?$/, use: "@griffel/webpack-loader" },
    ],
  },
};
```

Chain of loaders executes in reverse order. For TypeScript, install `@babel/preset-typescript`
and configure `babelOptions`.

### Babel preset

For library devs (not bundling):

```json
// .babelrc.json
{ "presets": ["@griffel/babel-preset"] }
```

Configure module source if re-exporting Griffel from a third party.

### What gets optimized

`makeStyles` runtime work (style resolution) happens once on first render. Build-time transform
pre-computes and replaces with lightweight `__styles` that concatenates classes and inserts
into DOM.

### Build-time evaluation

Styles may depend on other modules (tokens, helpers). Griffel batches evaluation in a single
context using Linaria's style evaluation. Additional language features (TS) need extra config.

## Unprocessed (raw) styles (v9.67.0+)

For multi-bundle apps, raw modules (`.raw.js`) prevent CSS class clashes. Configure bundler to
prioritize `.raw.js` resolution:

Webpack:
```js
resolve: { extensions: [".raw.js", ".js", ".tsx", ".ts"] }
```

Vite:
```js
resolve: { extensions: [".raw.js", ".js", ".tsx", ".ts"] }
```

Packages shipping raw modules: react-components, react-charts, react-icons, react-brand-icons,
fluentui-copilot. Backward compatible, opt-in.

## Native ESM

Packages are `"type": "module"`, dual build (ESM in `lib/`, CJS in `lib-commonjs/`). Enables
no-bundler usage (browser via import map / Node via .mjs) — demonstration only, not
production-ready (request waterfall, no tree-shaking, runtime style resolution, manual versioning).

Known gaps: tabster (no exports map), older react-icons builds (extensionless imports).
