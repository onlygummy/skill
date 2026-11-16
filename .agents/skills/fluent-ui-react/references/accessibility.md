# Fluent UI React — Accessibility

## Scope (what components handle)

- Semantic DOM structure (correct elements/roles)
- Valid ARIA attributes + state
- Keyboard navigation (tab, arrows, Enter/Space, Escape, Shift+F10)
- Screen reader navigation
- Touch interaction
- Focus handling (autofocus on open, Esc dismiss, focus trap for dialogs/popups)
- Color contrast
- Light/Dark/High Contrast themes
- Focus indicator on keyboard interaction
- Tested up to 400% zoom

Uses `tabster` for focus management; integrates with app-level tabster.

## Out of scope

- Internationalization, globalization, keyboard shortcuts, language detection (app responsibility)
- App-level focus handling (use tabster)

## Ensuring accessibility

- axe-core validates components during dev/build.
- Manual tests on isolated scenario pages by trusted testers + real users.

## Best practices

- Label components properly.
- Dialog: add `aria-describedby` on DialogSurface pointing to content (short confirmations);
  `aria-label`/`aria-labelledby` if no DialogTitle.
- Dialog must have at least one focusable element.
- Avoid nested dialogs (anti-pattern). Use multi-step wizard, sequential dialogs, or panels.
- When embedding Menu/Combobox/Dropdown/Popover inside a Dialog, set `aria-modal={false}` on
  DialogSurface (VoiceOver on iOS/macOS otherwise can't access popup).
- `DialogSurface` defaults `aria-describedby="dialog-content-id"`; for complex DialogContent set
  `aria-describedby={undefined}`.
- `disabledFocusable` for tab-order preservation in menus/commandbars.
- Buttons >= 24x24 for WCAG target size (default covers; SplitButton small variant is exception).
- Sentence-case labels; single verb + noun.

## Accessibility edge cases (Dialog)

1. NVDA reads dialog info twice
2. Talkback doesn't support dialog name/description
3. Talkback doesn't support `alertdialog`
4. Popups inside dialog need `aria-modal=false`
5. `aria-describedby` on DialogSurface

## Focus indicator

Visible focus ring on keyboard interaction. See focus indicator docs.

## Truncation

Handle text truncation carefully to not hide content from screen readers.

## Scenarios

Test scenarios: component labelling, button/checkbox/dropdown/input/menubutton/radiogroup/
spinbutton/splitbutton/textarea accessibility, debugging notifications, experiences, focus
indicator, notification best practices, truncation.
