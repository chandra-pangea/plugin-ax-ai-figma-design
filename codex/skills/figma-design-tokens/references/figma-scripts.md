# Figma scripts (`use_figma`) — idempotent token build

> Part of `figma-design-tokens`. All scripts run through `use_figma`. **Load `figma-use` first** and pass it in
> `skillNames`. Write plain JS with top-level `await`/`return` — no IIFE, no `figma.closePlugin()`. On error:
> STOP (scripts are atomic, nothing was written), read it, fix, retry. Touch only the token collections and
> their styles.

## Shared helpers (prepend to any build script)

```javascript
const TAG_NS = 'ax-dft';                 // shared-plugin-data namespace for idempotency
const UPDATE = false;                    // true ONLY when the user asks to overwrite existing values

function hexToRgba(hex, alpha) {
  hex = String(hex).replace('#', '').trim();
  if (hex.length === 3) hex = hex.split('').map(c => c + c).join('');
  if (hex.length === 8) { alpha = parseInt(hex.slice(6, 8), 16) / 255; hex = hex.slice(0, 6); }
  return { r: parseInt(hex.slice(0,2),16)/255, g: parseInt(hex.slice(2,4),16)/255,
           b: parseInt(hex.slice(4,6),16)/255, a: alpha === undefined ? 1 : alpha };
}

// Find-or-create a collection. Renames the first mode only when WE created it.
async function ensureCollection(name, modeName) {
  const cols = await figma.variables.getLocalVariableCollectionsAsync();
  let c = cols.find(x => x.name === name);
  if (c) return { collection: c, modeId: c.modes[0].modeId, created: false };
  c = figma.variables.createVariableCollection(name);
  c.renameMode(c.modes[0].modeId, modeName);
  return { collection: c, modeId: c.modes[0].modeId, created: true };
}

async function existingVarsIn(collection) {
  const all = await figma.variables.getLocalVariablesAsync();
  const map = {}; for (const v of all) if (v.variableCollectionId === collection.id) map[v.name] = v;
  return map;
}

// Idempotent raw-value create (primitives). type: 'COLOR'|'FLOAT'|'STRING'.
function makeEnsureVar(collection, modeId, existing) {
  return function ensureVar(name, type, value, scopes) {
    let v = existing[name];
    if (v) { if (UPDATE) { v.setValueForMode(modeId, value); return {name, id:v.id, status:'updated'}; }
             return {name, id:v.id, status:'skipped'}; }
    v = figma.variables.createVariable(name, collection, type);
    v.setValueForMode(modeId, value);
    if (scopes !== undefined) v.scopes = scopes;
    v.setSharedPluginData(TAG_NS, 'key', name);
    existing[name] = v;
    return {name, id:v.id, status:'created'};
  };
}
```

## Discover (Phase 0) — read-only

```javascript
const cols = await figma.variables.getLocalVariableCollectionsAsync();
const all = await figma.variables.getLocalVariablesAsync();
function report(name) {
  const c = cols.find(x => x.name === name);
  if (!c) return { name, exists: false, vars: [] };
  return { name, exists: true, id: c.id, modes: c.modes.map(m=>m.name),
           vars: all.filter(v => v.variableCollectionId === c.id).map(v=>v.name).sort() };
}
return { brand: report('BrandPrimitives'), app: report('App-Primitives'),
         semantics: report('Semantics'), componentTokens: report('ComponentTokens') };
```

## BrandPrimitives (Phase 1)

```javascript
// <helpers>
const BRAND = { colors: [/* {name,hex[,alpha]} */], families: [], weights: [], sizes: [], lineHeights: [], letterSpacing: [] };
const COLOR_SCOPES = ['FRAME_FILL','SHAPE_FILL','TEXT_FILL','STROKE_COLOR'];
const { collection, modeId, created } = await ensureCollection('BrandPrimitives', 'Value');
const existing = await existingVarsIn(collection);
const ensureVar = makeEnsureVar(collection, modeId, existing);
const out = [];
for (const c of BRAND.colors)        out.push(ensureVar(c.name, 'COLOR', hexToRgba(c.hex, c.alpha), COLOR_SCOPES));
for (const f of BRAND.families)      out.push(ensureVar(f.name, 'STRING', f.value, ['FONT_FAMILY']));
for (const w of BRAND.weights)       out.push(ensureVar(w.name, 'FLOAT',  w.value, ['FONT_WEIGHT']));
for (const s of BRAND.sizes)         out.push(ensureVar(s.name, 'FLOAT',  s.value, ['FONT_SIZE']));
for (const l of BRAND.lineHeights)   out.push(ensureVar(l.name, 'FLOAT',  l.value, ['LINE_HEIGHT']));
for (const t of BRAND.letterSpacing) out.push(ensureVar(t.name, 'FLOAT',  t.value, ['LETTER_SPACING']));
return { collection: 'BrandPrimitives', created, out };
```

## App-Primitives (Phase 2) — one chunk per script

Ensure the collection once (`ensureCollection('App-Primitives','Value')`), then run chunks and validate each.
Fill numeric sets from the **verified** catalog values.

```javascript
// Chunk A — gray + semantic ramps (COLOR_SCOPES). Build the FULL 50..900 for each semantic ramp.
// Chunk B — spacing/radius/border:
const spacing = {'0':0,'25':2,'50':4,'75':6,'100':8,'150':12,'200':16,'250':20,'300':24,'400':32,'500':40,'600':48,'800':64,'1000':80};
const radius  = {none:0,sm:2,md:4,lg:8,xl:12,'2xl':16,full:9999};
const border  = {none:0,default:1,thick:2,thicker:3};
for (const [k,v] of Object.entries(spacing)) ensureVar(`spacing/${k}`,'FLOAT',v,['GAP','WIDTH_HEIGHT']);
for (const [k,v] of Object.entries(radius))  ensureVar(`radius/${k}`,'FLOAT',v,['CORNER_RADIUS']);
for (const [k,v] of Object.entries(border))  ensureVar(`border/width/${k}`,'FLOAT',v,['STROKE_FLOAT']);
// Chunk C — elevation (decomposed): per level y/blur/spread FLOAT ['EFFECT_FLOAT'] + color COLOR ['EFFECT_COLOR'].
// Chunk D — opacity ['OPACITY'] / motion duration+easing [] / z-index [] / breakpoint [].
```

## Alias variables — Semantics (Phase 3) & ComponentTokens (Phase 4)

```javascript
// <helpers>
const all = await figma.variables.getLocalVariablesAsync();
const byName = {}; for (const v of all) byName[v.name] = v;   // resolve primitives/semantics by name
const primTarget = n => { const v = byName[n]; if (!v) throw new Error('Missing alias target: ' + n); return v; };

const { collection: sem, modeId } = await ensureCollection('Semantics', 'Default');
const existing = await existingVarsIn(sem);
function makeAlias(name, type, targetName, scopes) {
  if (existing[name]) return { name, id: existing[name].id, status: 'skipped' };
  const v = figma.variables.createVariable(name, sem, type);
  v.setValueForMode(modeId, { type: 'VARIABLE_ALIAS', id: primTarget(targetName).id });
  v.scopes = scopes;
  v.setSharedPluginData('ax-dft', 'key', name);
  v.setSharedPluginData('ax-dft', 'kind', 'semantic');
  existing[name] = v;
  return { name, id: v.id, status: 'created' };
}
// ComponentTokens: same pattern, collection 'ComponentTokens', aliasing Semantics variables (one hop).
```

- A missing alias target is a **hard stop** — report the exact name and add the primitive first; never
  substitute a raw value. Chunk ≤ ~15 aliases per script; return `{created, skipped, failed}`.

## Styles (Phase 4)

```javascript
// Text style bound to typography variables:
const ts = figma.createTextStyle(); ts.name = 'DS/H1';
await figma.loadFontAsync({ family, style });          // resolve family/weight from the typography variables first
ts.setBoundVariable('fontFamily', famVar); ts.setBoundVariable('fontSize', sizeVar);
ts.setBoundVariable('fontWeight', weightVar); ts.setBoundVariable('lineHeight', lhVar);
// Effect style: figma.createEffectStyle(); one DROP_SHADOW; bind offsetY/radius/spread/color from the
// elevation/<role>/* variables (see figma-use for the current effect-binding API). If effect binding is
// unavailable, set values read from the variables and note the style must be regenerated if elevation changes.
```

## Validate (Phase 5) — read-only

```javascript
const cols = await figma.variables.getLocalVariableCollectionsAsync();
const all = await figma.variables.getLocalVariablesAsync();
function summarize(name) {
  const c = cols.find(x => x.name === name);
  if (!c) return { name, exists: false };
  const vars = all.filter(v => v.variableCollectionId === c.id);
  const groups = [...new Set(vars.map(v => v.name.split('/').slice(0,-1).join('/')))].sort();
  const raw = vars.filter(v => { const m = Object.values(v.valuesByMode)[0]; return m && m.type !== 'VARIABLE_ALIAS'; }).length;
  return { name, count: vars.length, groups, allScopesLeak: vars.filter(v => (v.scopes||[]).includes('ALL_SCOPES')).map(v=>v.name), rawValues: raw };
}
return { brand: summarize('BrandPrimitives'), app: summarize('App-Primitives'),
         semantics: summarize('Semantics'), componentTokens: summarize('ComponentTokens'),
         other: cols.map(c=>c.name).filter(n => !['BrandPrimitives','App-Primitives','Semantics','ComponentTokens'].includes(n)) };
```

- `allScopesLeak` must be empty. For `Semantics`/`ComponentTokens`, `rawValues` must be 0 (all aliases).

## Re-scope primitives (Phase 5, gated)

On explicit confirmation only: `for (const v of primitives) v.scopes = [];` — scopes only, never values/names.
Return the count re-scoped.

## Optional — code syntax (offer, off by default)

```javascript
for (const v of tokenVars) v.setVariableCodeSyntax('WEB', `var(--${v.name.replace(/[\s\/]+/g,'-').toLowerCase()})`);
```
