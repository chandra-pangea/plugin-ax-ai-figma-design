# Quality checklist (Phase 6) — all passes required before presenting

Screenshot as you check. Fix every violation and re-check; report honestly, including accepted trade-offs.

## A. Token binding
- [ ] Every variable used on a component is a `Semantics`/`ComponentTokens` token or a `DS/*` style — no raw
      values, no primitive bindings (script-scan all `DS_Core_*` nodes).
- [ ] No broken alias chain (every token resolves to a primitive).
- [ ] Elevation via the `DS/Elevation/*` effect styles; type via `DS/*` text styles.

## B. Per-component (every set, every variant)
- [ ] Full variant matrix present (count matches the catalog; property names/values exact).
- [ ] Auto-layout resizes sanely when text lengthens (test one long-label variant per set).
- [ ] States visually distinct AND consistent: every Hover shifts the same way; Focus always shows
      `color/focus/ring` at `border-width/focus`; Disabled always uses the disabled tokens (not opacity-faked
      unless the catalog says so).
- [ ] Touch/click targets ≥ the look's minimum (default 40px for md controls); icons have padding, not tiny frames.
- [ ] Patterns contain atom **instances** (no detached copies, no redrawn lookalikes).
- [ ] Doc frame present: name, base-DS mapping, usage line, do/don't.
- [ ] Real content everywhere — no lorem, no "Label".

## C. System
- [ ] **WCAG AA** on every interactive state: text on surfaces; action text on action bg (all states); status
      text on status bg-subtle and text-on-bold on bg-bold; link on page; selected/text on selected/bg. Record
      the ratio table.
- [ ] **Conformance**: place Button, Card, Input, NavItem, Modal beside the look's screens — indistinguishable
      in personality (radius, density, color roles, elevation, type). Any seam = violation.
- [ ] Cross-component consistency: one control height per size across Button/Input/Select; one radius per role;
      identical focus treatment; one icon family.
- [ ] Parity table has a row for every built component, gaps resolved (not blank).
- [ ] Templates use the look's nav model and grid; slots clearly marked.
- [ ] Only `DS_Core_Components|Patterns|Templates` were modified — nothing else in the file.

## D. Report
Counts built / existing / missing / deferred per tier · WCAG ratio table · conformance screenshots · parity
gaps resolved · deferrals with reasons.
