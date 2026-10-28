# Figma build guide — `use_figma` patterns for screens

> Part of `figma-design-screens`. Load `figma-use` AND `figma-generate-design` before any `use_figma` call (add
> `figma-generate-library` when creating a component), and include them in `skillNames`. Scripts are atomic —
> on error nothing was written: read it, fix, retry. **`return` created node IDs and screenshot after every chunk.**

## Target page
- **Page link** → build there. **File link / no page** → create a page: `Rollout_<slug>` (Rollout) or
  `Design_Direction_<name>` (Direction). If the name exists, confirm: append, or `_v2`.
- Never build on a `DS_Core_*` page (read-only here except via the gap protocol).

## Page structure (deterministic)
```
Rollout_<slug>                    (page)
├─ 00 · Cover                     (scope, requirement source, direction, date, screen index)
├─ Section: <Screen name>
│   ├─ 01 · <Screen>              (default — always first)
│   ├─ 01 · <Screen> — empty
│   ├─ 01 · <Screen> — loading
│   ├─ 01 · <Screen> — error: <what>
│   └─ …one frame per applicable state, canonical order
└─ Section: <Next screen>  …
```
One section per screen; default first; states left-to-right. Frame numbering continues across runs (read the
highest existing number first). Frame size = the look's declared platform size.

## The two golden rules
1. **Everything is an auto-layout frame** (no absolute positioning except deliberate overlays).
2. **Everything visual is bound to a variable.** Rollout → `Semantics`/`ComponentTokens` or a `DS/*` style.
   [Direction] → a **primitive** variable. Catch yourself typing a hex or px → find the token, or flag the gap.

## Assembly order (per screen — never batch unverified work)
1. **Start from the template** (Rollout): instance the DS app-shell/layout template; set the nav active state
   via component properties. Never rebuild a shell. ([Direction]: build the shell once as a base frame, copy it.)
2. **Fill regions with instances** — one region at a time, overriding via component properties.
3. **Bind every remaining property** (the composition's own frames) to tokens.
4. **Screenshot** and check layout, hierarchy, bindings, instance integrity.
5. Next region, then next screen. **Repeats** (rows, cards): build one, verify, copy — never redraw per row.

## Binding patterns
```js
// Resolve variables by name once (build a lookup from Phase 1)
const all = await figma.variables.getLocalVariablesAsync();
const V = {}; for (const v of all) V[v.name] = v;
const byName = n => { const v = V[n]; if (!v) throw new Error('Missing variable: ' + n); return v; };

// fills / strokes
node.fills = [ figma.variables.setBoundVariableForPaint({ type:'SOLID', color:{r:0,g:0,b:0} }, 'color', byName('color/surface/raised')) ];
// numbers
node.setBoundVariable('paddingLeft', byName('space/inset/md'));
node.setBoundVariable('itemSpacing', byName('space/stack/md'));
for (const f of ['topLeftRadius','topRightRadius','bottomLeftRadius','bottomRightRadius']) node.setBoundVariable(f, byName('radius/surface'));
// text — DS style + bound color
await textNode.setTextStyleIdAsync(styleId);   // 'DS/Body'
// typography (Direction mode, direct to primitives):
await figma.loadFontAsync({ family:'Inter', style:'Semibold' });   // MUST load before characters/size
t.setBoundVariable('fontSize', byName('font/size/xl'));
```
- Rollout: a variable resolving to a **primitive** collection is a defect — use a semantic/component token, or
  add one via the gap protocol.
- Elevation (Rollout) comes from the `DS/Elevation/*` effect styles. [Direction]: assemble a DROP_SHADOW from
  the decomposed `elevation/{level}/y|blur|spread|color` primitives via `setBoundVariableForEffect`.

## Instances & overrides (Rollout)
```js
const comp = await figma.importComponentByKeyAsync(key);   // or find the set on the DS page
const inst = comp.createInstance();
inst.setProperties({ State:'Disabled', 'Label#1:0':'Create project' });
```
Change appearance only through `setProperties`/variant selection. Overriding an instance's fill/radius/padding
is a defect. Pick the correct **state variant** for state frames. `detachInstance()` is forbidden.

## Prototype reactions (Phase 7)
```js
await node.setReactionsAsync([{
  trigger: { type: 'ON_CLICK' },
  actions: [{ type: 'NODE', navigation: 'NAVIGATE', destinationId: targetFrame.id,
    transition: { type: 'SMART_ANIMATE', easing: { type: 'EASE_OUT' }, duration: 0.2 } }]
}]);
```
Read `motion/duration/*` and `motion/easing/*` **values** and use those numbers. Overlays use
`navigation:'OVERLAY'`/`'CLOSE'`. Smart Animate matches by **layer name** — keep names identical across animated
frames. Set the page's prototype starting frame to the default screen.

## Tagging & idempotency
```js
node.setSharedPluginData('ax_vds','key', node.name);
node.setSharedPluginData('ax_vds','screen', screenName);
node.setSharedPluginData('ax_vds','state', stateName);   // 'default' for the default frame
```
Discovery on a re-run = enumerate the page's frames and read these keys. Skip-by-default: an existing
screen+state is `existing`, never silently overwritten. Library additions made via the gap protocol are tagged
with the library namespace (`ax-dsc`), not this one.

## Audits (Phase 8 gates) — read-only over every node on the screen page
1. **Raw-value / primitive-binding** — flag: a solid paint with no bound variable; a bound variable resolving
   to a **primitive** (Rollout); a radius/stroke/padding/gap/fixed-size with no bound variable (allow `0` and
   nodes named `slot`/`spacer`); a text node with no `DS/*` style or an unbound text color.
2. **Instance integrity** (Rollout) — flag: a node named like a DS component that is a `FRAME`/`GROUP` not an
   `INSTANCE`; an `INSTANCE` with local paint/radius/padding overrides; a detached instance; an instance whose
   main component is missing.

Return `{node, frame, issue}` rows, print counts, fix, re-run until zero.

## Chunking
One script per: page/section setup · one screen's shell · one screen region · one state frame · one batch of
reactions. Screenshot between them.
