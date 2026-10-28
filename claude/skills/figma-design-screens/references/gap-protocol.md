# Gap protocol — growing the system without corrupting it

> Part of `figma-design-screens`, Phase 3 (Rollout mode). A screen may grow the design system. It may not grow
> a *second* design system on a screen page.

## The reuse ladder (walk it in order)

1. **Use the existing component.** Read its variant properties first — most "missing" components are an unread
   variant.
2. **Compose existing components.** Many needs are a pattern, not a part (a card containing a table). Compose on
   the screen; promote to a component only if it repeats across screens.
3. **Add a variant to the existing set.** A new size/state/density/layout/emphasis is a **variant** — the
   preferred way to grow. `Variant=Compact` beats a new `TableCompact`.
4. **Create a new component.** Only when the anatomy is genuinely new. State in the plan why step 3 failed.
5. **Stop and escalate.** A need with no primitive behind it → route to `figma-design-tokens`. Never invent a
   raw value.

## Token gaps
- A missing **semantic** role → create it as an **alias to a primitive** (never a raw value).
- A missing **component token** → alias a semantic (one hop), matching the naming convention exactly.
- No primitive to alias → **stop and escalate** to `figma-design-tokens`.

## Where additions live (never loose on the screen page)

| Addition | Home |
|---|---|
| New variant | the existing component set, in place |
| New atom | `DS_Core_Components` |
| New pattern (composes atoms) | `DS_Core_Patterns` |
| New template | `DS_Core_Templates` |
| New semantic / component token | the matching variable collection |

## How to build an addition (match the library's conventions)
- Token-bound throughout — zero raw values, zero primitive bindings.
- Full variant matrix, auto-layout, real labels, correct resizing.
- Composed from **instances** (a new pattern contains the real atoms).
- Documentation frame beside the set (name, purpose, usage note, base-DS mapping).
- Tagged `node.setSharedPluginData('ax-dsc','key', node.name)` so it reads as existing on future runs.
- Placed in the page's existing grid/section rhythm.

## Approval gate
Before building any addition, present the **complete gap list** in one table: need · why existing parts don't
cover it · proposed resolution (variant / component / token) · where it lives · which screens consume it. Get a
go-ahead, build the additions **before** the screens that use them, so no screen is assembled from placeholders.
Never overwrite an existing token/component — additions are additive; a change to something that exists needs
the user's explicit instruction.
