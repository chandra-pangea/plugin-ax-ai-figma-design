# Figma build guide — `use_figma` patterns for the component library

> Part of `figma-design-components`. Load `figma-use` AND `figma-generate-library` before any `use_figma` call
> and include `figma-design-components,figma-use,figma-generate-library` in `skillNames`. Scripts are atomic —
> on error nothing was written: read it, fix, retry. **Always `return` created node/style IDs** and screenshot
> after every set.

## Tagging (idempotency)
- Component sets: `node.setSharedPluginData('ax-dsc','key', node.name)` and `('ax-dsc','tier','1'|'2'|'3')`.
- Discovery = enumerate `DS_Core_*` pages and read these keys. Skip-by-default: a tagged set that exists counts
  as `existing`, never recreated.

## Building a component set (one script per set)
1. Build **one variant frame per matrix cell** with auto-layout; name each `Prop1=Value, Prop2=Value` (exact
   catalog names/values).
2. **Bind everything**:
   - fills/strokes: `node.fills = [figma.variables.setBoundVariableForPaint({type:'SOLID', color:{r:0,g:0,b:0}}, 'color', tokenVar)]`
   - numbers (`paddingLeft`, `itemSpacing`, `cornerRadius`, `strokeWeight`, fixed `width/height`): `node.setBoundVariable('paddingLeft', tokenVar)`
   - text: `await textNode.setTextStyleIdAsync(dsStyleId)` (`DS/Body` etc.) + a bound fill color.
   - A variable that resolves to a **primitive** collection on a component is a defect — bind a
     `Semantics`/`ComponentTokens` token, or add one via `figma-design-tokens`.
3. `figma.combineAsVariants(frames, page)` → name the set → tag it → wrap in a **doc frame** (title, base-DS
   mapping line, usage sentence, do/don't).
4. Lay doc frames on a page grid (64px gutters, one row per catalog section; section-header text frames).
5. `return { setId, variantCount, docFrameId }` and **screenshot** before the next script.

## Patterns & templates — instances only
- A pattern/template contains atom/pattern **instances**: `const inst = comp.createInstance(); inst.setProperties({ State:'Disabled', 'Label#1:0':'Create project' })`.
- Change appearance only through `setProperties` / variant selection. Overriding an instance's fill/radius/
  padding is a defect. `detachInstance()` is forbidden.
- Elevation comes from the `DS/Elevation/*` effect styles — don't hand-build shadows.

## §Audit — run at each tier end (read-only)
Walk every `DS_Core_*` node and flag:
- any solid paint (fill or stroke) with no bound variable;
- any bound variable resolving to a **primitive** collection (should be semantic/component);
- any corner radius / stroke weight / padding / gap / fixed size with no bound variable (allow `0` and nodes
  explicitly named `slot`/`spacer`);
- any text node with no `DS/*` text style, or an unbound text color;
- any pattern/template child that matches a component name but is a `FRAME`/`GROUP` not an `INSTANCE`
  (hand-drawn lookalike), or a detached instance.

Return `{node, page, set, issue}` rows, print counts, fix, re-run until zero. Then diff the tier's built sets
against the catalog rows: print `built / existing / missing`; build the missing or record a deferral.
