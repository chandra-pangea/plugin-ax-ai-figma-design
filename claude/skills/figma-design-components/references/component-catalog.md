# Component catalog

> Part of `figma-design-components`. The target set of components to build, each as a Figma component set with
> its **full variant matrix**. Build every row (or record it deferred, with a reason); a row already present is
> skipped + reported. Don't build a "representative subset".

Totals (completeness guide): Atoms **26 sets** · Patterns **23 sets** (+ look delta) · Templates **9 frames**.

Conventions:
- **Set name** exactly as listed. Variant properties use Title-case names and the exact values
  (`State=Default|Hover|Pressed|Focus|Disabled`).
- **Every** visual property bound to a `Semantics`/`ComponentTokens` variable or a `DS/*` style — zero
  hardcoded values. The "Bind" line names the load-bearing bindings; anything unnamed still binds (the audit
  catches leaks).
- **Auto-layout** everywhere; text uses `DS/*` styles; icons from one family at `size/icon/*`.
- **Base-DS** column shows the wrapper mapping for Material 3 (M) / shadcn (S); other systems resolve the
  equivalent in the parity table.
- Each set in a **doc frame**: title, base-DS mapping, one usage sentence, one do/don't.
- Sizes default to `md`; size variants only where listed.

## Tier 1 — Atoms (`DS_Core_Components`) — 26

| # | Set | Variant matrix | Bind highlights | Base DS (M / S) |
|---|---|---|---|---|
| 1 | `Button` | Tone=Primary\|Secondary\|Ghost\|Danger × Size=Sm\|Md\|Lg × State=Default\|Hover\|Pressed\|Focus\|Disabled\|Loading × Icon=None\|Left\|Right (Loading only Primary\|Danger; Icon only Size=Md) | `button/height-*`, `button/pad-x-*`, `button/gap`, `button/radius`, `color/action/<tone>/*`, focus = `color/focus/ring` + `border-width/focus`, `DS/Label` | Button / button |
| 2 | `IconButton` | Tone(4) × Size=Sm\|Md\|Lg × State(5) | square `button/height-*`, `color/action/*`, `size/icon/*` | IconButton / button size=icon |
| 3 | `Link` | Kind=Inline\|Standalone × State=Default\|Hover\|Disabled | `color/text/link`, `link-hover` | — (anchor) |
| 4 | `Input` | State=Default\|Hover\|Focus\|Filled\|Error\|Disabled\|ReadOnly × Adornment=None\|IconLeft\|IconRight | all `input/*`; error = `input/border-error` + `status/danger/*` | TextField / input |
| 5 | `Textarea` | State=Default\|Focus\|Error\|Disabled | `input/*` | TextField multiline / textarea |
| 6 | `Select` (trigger) | State=Default\|Hover\|Focus\|Error\|Disabled\|Filled | `input/*` + chevron `color/icon/subtle` | Select / select trigger |
| 7 | `Checkbox` | Checked=Off\|On\|Indeterminate × State=Default\|Hover\|Disabled\|Error | `radius/control-sm`, on = `color/action/primary/bg`, check `text/on-brand` | Checkbox / checkbox |
| 8 | `Radio` | Checked=Off\|On × State=Default\|Hover\|Disabled | as Checkbox, circular | Radio / radio-group item |
| 9 | `Switch` | Checked=Off\|On × State=Default\|Hover\|Disabled | track on `action/primary/bg`, off `border/strong`; thumb `surface/raised` | Switch / switch |
| 10 | `Slider` | State=Default\|Hover\|Drag\|Disabled | track `surface/sunken`, fill `action/primary/bg`, thumb `surface/raised`+`elevation/raised` | Slider / slider |
| 11 | `Badge` | Tone=Neutral\|Brand\|Success\|Warning\|Danger\|Info × Emphasis=Subtle\|Bold | `badge/*` + `color/status/<tone>/*` | Badge / badge |
| 12 | `Tag` | State=Default\|Hover\|Selected\|Disabled × Removable=No\|Yes | `selected/*`, `badge/radius`, close `icon/subtle` | Chip / composite |
| 13 | `Avatar` | Size=Sm\|Md\|Lg × Content=Image\|Initials\|Icon | `radius/pill`, initials bg `surface/brand-subtle`, `DS/Label` | Avatar / avatar |
| 14 | `Tooltip` | Placement=Top\|Bottom\|Left\|Right | `surface/inverse`, `text/inverse`, `radius/control-sm`, `space/inset/xs·sm` | Tooltip / tooltip |
| 15 | `Divider` | Orientation=Horizontal\|Vertical × Emphasis=Subtle\|Default | `border/subtle·default`, `border-width/default` | Divider / separator |
| 16 | `Spinner` | Size=Sm\|Md\|Lg | `color/icon/brand`, `size/icon/*` | CircularProgress / — |
| 17 | `ProgressBar` | Kind=Determinate\|Indeterminate | track `surface/sunken`, fill `action/primary/bg`, `radius/pill` | LinearProgress / progress |
| 18 | `Skeleton` | Shape=Text\|Rect\|Circle | `surface/sunken`, `radius/control-sm·pill` | — / skeleton |
| 19 | `Kbd` | (single) | `surface/sunken`, `border/default`, `DS/Code`, `radius/control-sm` | — / kbd |
| 20 | `FieldLabel` | Requirement=None\|Required\|Optional | `DS/Label`, `text/primary`, required mark `status/danger/text` | InputLabel / label |
| 21 | `HelperText` | Tone=Help\|Error\|Success | `DS/Caption`, `text/tertiary` \| `status/danger/text` \| `status/success/text` | FormHelperText / — |
| 22 | `SegmentedControl` | Segments=2\|3 × Selected=1st\|2nd\|3rd | container `surface/sunken` `radius/control`, selected `surface/raised`+`elevation/raised` | SegmentedButton / toggle-group |
| 23 | `NotificationDot` | Kind=Dot\|Count | `status/danger/bg-bold`, `text-on-bold`, `radius/pill` | Badge / — |
| 24 | `Icon` (wrapper) | Size=Sm\|Md\|Lg | `size/icon/*`, fill `color/icon/default` | Icon / lucide |
| 25 | `Logo` (slot) | Size=Sm\|Md | brand asset placeholder, height via `size/*` | — |
| 26 | `TextPair` | Direction=Vertical\|Horizontal | `DS/Caption` `text/tertiary` + `DS/Body` `text/primary`, `space/stack/xs` | — (list primitive) |

## Tier 2 — Patterns (`DS_Core_Patterns`) — 23 + look delta

Patterns **compose Tier-1 instances** — never redraw an atom.

| # | Set | Variant matrix / contents | Bind highlights | Base DS (M / S) |
|---|---|---|---|---|
| 1 | `FormField` | Control=Input\|Textarea\|Select × State=Default\|Error\|Disabled | `FieldLabel` + control + `HelperText`, gaps `input/gap-label·gap-help` | FormControl / form field |
| 2 | `SearchBar` | State=Default\|Focus\|Filled | `Input` + search icon + optional `Kbd` | SearchBar / composite |
| 3 | `MenuItem` | State=Default\|Hover\|Selected\|Disabled × Kind=Default\|Danger\|WithIcon\|WithShortcut | `space/inset/sm`, hover `action/ghost/bg-hover`, selected `selected/*`, danger `status/danger/text` | MenuItem / dropdown-menu item |
| 4 | `Menu` | Size=Sm\|Md | `surface/overlay`, `radius/control` (or `overlay`), `DS/Elevation/Overlay`, `MenuItem`s + divider | Menu / dropdown-menu content |
| 5 | `Card` | Kind=Default\|Header\|HeaderFooter × Interactive=No\|Hover\|Selected | all `card/*`; interactive = `DS/Elevation/Raised` or border shift | Card / card |
| 6 | `StatTile` | Trend=None\|Up\|Down | `Card` + `DS/Display|H2` value + `TextPair`; trend `status/success·danger/text` | — / composite |
| 7 | `TableHeaderCell` | Sort=None\|Asc\|Desc | `table/header-bg·header-text`, `DS/Label`, sort icon `icon/subtle` | TableHead / th |
| 8 | `TableCell` | Content=Text\|Secondary\|Badge\|Actions\|Checkbox | `table/cell-pad-x`, `DS/Body-sm` | TableCell / td |
| 9 | `TableRow` | State=Default\|Hover\|Selected × Density=Default\|Compact | `table/row-*` | TableRow / tr |
| 10 | `Table` | (header + 4 rows + footer w/ `Pagination`) | composed | Table / table |
| 11 | `TabItem` | State=Default\|Hover\|Active\|Disabled | `tabs/*`, indicator `tabs/indicator-*` | Tab / tabs trigger |
| 12 | `Tabs` | (bar of TabItems, first active) | `tabs/gap`, bottom `border/subtle` | Tabs / tabs list |
| 13 | `Breadcrumb` | Depth=2\|3 | `DS/Body-sm`, links `text/link`, current `text/primary`, sep `icon/subtle` | Breadcrumbs / breadcrumb |
| 14 | `Pagination` | State=First\|Middle\|Last | `IconButton Ghost` + page `DS/Body-sm`, current `selected/*` | Pagination / pagination |
| 15 | `Accordion` | State=Collapsed\|Expanded | header `DS/H4`, chevron, `border/subtle`, `space/inset/md` | Accordion / accordion |
| 16 | `Alert` | Tone=Info\|Success\|Warning\|Danger × Dismissible=No\|Yes | `status/<tone>/bg-subtle·border·icon·text`, `radius/surface` | Alert / alert |
| 17 | `Toast` | Tone=Neutral\|Success\|Danger × Action=No\|Yes | `surface/inverse`+`text/inverse` (or per look), `DS/Elevation/Overlay` | Snackbar / sonner |
| 18 | `Modal` | Size=Sm\|Md\|Lg × Kind=Default\|Confirm\|Destructive | `modal/*`, `DS/Elevation/Modal`, footer real `Button`s (Destructive → `Button Danger`) | Dialog / dialog |
| 19 | `Drawer` | Side=Right\|Left | `surface/overlay`, `DS/Elevation/Modal`, width per look, header + close `IconButton` | Drawer / sheet |
| 20 | `Popover` | (single, optional arrow) | `surface/overlay`, `radius/control`, `DS/Elevation/Overlay`, `space/inset/md` | Popover / popover |
| 21 | `EmptyState` | Kind=Default\|Error\|NoResults | icon/illustration slot, `DS/H3` + `DS/Body` `text/secondary`, CTA `Button` | — / composite |
| 22 | `NavItem` + `SidebarNav` | NavItem: State=Default\|Hover\|Selected\|Disabled × Badge=No\|Yes; SidebarNav: grouped sections + `Logo` + user row | all `nav/*` | NavigationDrawer / composite |
| 23 | `TopBar` | Kind=Default\|WithSearch\|WithActions | height `size/control/lg`, `surface` per look, composed `SearchBar`/`IconButton`/`Avatar` | TopAppBar / composite |

**Look delta (conditional additions):** every pattern the chosen look's screens show that isn't above (command
palette, wizard/stepper header, file-upload dropzone, calendar/date field, stage/canvas chrome) becomes a
named set with a variant matrix you define from those screens. `Stepper`, `FileUpload`, `DateField`+`Calendar`
are required whenever the product involves multi-step flows, uploads, or dates respectively; otherwise record
as deferred (not silently dropped).

## Tier 3 — Templates (`DS_Core_Templates`) — 9

Frames assembled from pattern/atom **instances** at the look's platform size, with labelled slot placeholders.

| # | Template | Contents |
|---|---|---|
| 1 | `Template/AppShell` | The look's nav model: `SidebarNav` + `TopBar` + content slot on `surface/page` |
| 2 | `Template/Dashboard` | AppShell + `StatTile` row + 2 `Card` modules + `Table` preview |
| 3 | `Template/ListPage` | AppShell + page header + filter row (`SearchBar`, `Select`, `Tag`) + full `Table` + `Pagination` |
| 4 | `Template/DetailPage` | AppShell + `Breadcrumb` + header (avatar/title/badges/actions) + `Tabs` + two-column content |
| 5 | `Template/FormPage` | AppShell + form column: `FormField` stack, section dividers, sticky footer `Button`s |
| 6 | `Template/SettingsPage` | AppShell + secondary nav (vertical `NavItem`s) + sectioned `Card`s of `FormField`s |
| 7 | `Template/AuthEntry` | `Logo`, `FormField`s, primary `Button`, link row — centered/split per look |
| 8 | `Template/OverlayInContext` | `Template/ListPage` dimmed by `color/overlay/scrim` @ `opacity/scrim` with `Modal Md` open |
| 9 | `Template/EmptyErrorPage` | AppShell + full-page `EmptyState` (one Default, one Error) |

## Build protocol
1. Before each tier, print its rows as a checklist with the variant count you'll build for each.
2. Build **one set per `use_figma` script**; record its node ID.
3. At tier end, run the audit (`figma-build.md` §Audit): enumerate tagged components, print built / existing /
   missing; build the missing or record a deferral with a reason.
