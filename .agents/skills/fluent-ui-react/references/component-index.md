# Fluent UI React — Component Index

Full list of components documented in the Fluent UI React v9 Storybook.

## Stable (in `@fluentui/react-components`)

- Accordion
- Avatar, AvatarGroup
- Badge (Badge, CounterBadge, PresenceBadge)
- Breadcrumb
- Button (Button, CompoundButton, MenuButton, SplitButton, ToggleButton)
- Card (Card, CardFooter, CardHeader, CardPreview)
- Carousel (Carousel, CarouselNav)
- Checkbox
- ColorPicker
- Combobox
- DataGrid
- Dialog (Dialog, DialogTrigger, DialogSurface, DialogTitle, DialogContent, DialogBody, DialogActions)
- Divider
- Drawer
- Dropdown
- Field
- FluentProvider
- Image
- InfoLabel
- Input
- Label
- Link
- List
- Menu (Menu, MenuList, MenuDivider, MenuGroup, MenuItem, ...)
- MessageBar
- Nav
- Overflow (Overflow, OverflowItem)
- Persona
- Popover (Popover, PopoverSurface, PopoverTrigger)
- Portal (Portal, toMountNodeProps)
- ProgressBar
- RadioGroup
- Rating, RatingDisplay
- SearchBox
- Select
- Skeleton
- Slider
- SpinButton
- Spinner
- SwatchPicker
- Switch
- Table
- TabList
- Tag (Tag, TagGroup, InteractionTag), TagPicker
- TeachingPopover
- Text
- Textarea
- Toast
- Toolbar
- Tooltip
- Tree

## Compat (v8 bridge, `-compat` suffix)

- Calendar, DatePicker, TimePicker

## Preview (preview packages / preview components)

- Calendar (preview), MenuGrid, RangeSlider
- Various preview packages (`@fluentui/react-*-preview`)

## Motion (preview)

- APIs: createMotionComponent, createMotionComponentVariant, createPresenceComponent, motionSlot,
  presenceMotionSlot, PresenceGroup
- Components: Atoms, Blur, Collapse, Fade, Rotate, Scale, Slide, Stagger
- Choreography: Stagger

## Migration shims

v0: Attachment, ButtonMixins, FlexShim, FormFieldShim, GridShim, InputMixins, List, SegmentShim,
SliderMixin, SpinnerMixins, Video.
v8: ActionButton/CommandButton/CompoundButton/DefaultButton/MenuButton/PrimaryButton/ToggleButton
shims, CheckboxShim, StackShim, Theme shims (createBrandVariants, createV8Theme, createV9Theme).

## Utilities

- ARIA live: AriaLiveAnnouncer, useAnnounce, useTypingAnnounce
- Focus management: useArrowNavigationGroup, useFocusableGroup, useFocusFinders, useModalAttributes,
  useObservedElement, useRestoreFocusSource/Target, useUncontrolledFocus
- Positioning: useSafeZoneArea
- Theme: createCSSRuleFromTheme

## Themes & tokens

Web Light/Dark, Teams Light/Dark/High Contrast (legacy). Tokens: colors, fonts, spacing, typography,
shadows, border radii, stroke widths. Theme designer available.

## Each component's docs include

Props table, subcomponents, best practices, accessibility notes, SSR notes, and code examples.
Consult the Storybook at https://react.fluentui.dev/ or llms.txt for full per-component detail.
