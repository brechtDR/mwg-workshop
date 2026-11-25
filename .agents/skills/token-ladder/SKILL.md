---
name: token-ladder
description: How this workshop styles components with its 3-tier design tokens, modern CSS standards, and cascade layers. Use whenever writing or reviewing CSS in an exercise, after Modern Web Guidance has told you which platform feature to use.
---

# Token Ladder & Modern CSS Standards

Modern Web Guidance tells you _which platform feature_ to use. This skill tells you _how we style and architect it here_.

---

## 1. Three-Tier Token Architecture

Tokens flow in one strict direction: **Primitives (`--p-*`) -> Semantics (`--s-*`) -> Component (`--c-*`)**. Never skip levels.

| Tier          | Prefix  | Purpose                                                                                                | Allowed Consumers                 |
| ------------- | ------- | ------------------------------------------------------------------------------------------------------ | --------------------------------- |
| **Primitive** | `--p-*` | Raw, context-free values (e.g. `--p-color-neutral-900`, `--p-space-2`, `--p-radius-md`)                | Only `shared/tokens.css`          |
| **Semantic**  | `--s-*` | Primitives mapped to purpose (e.g. `--s-color-surface-elevated`, `--s-space-compact`, `--s-font-body`) | Component stylesheets             |
| **Component** | `--c-*` | Public theming overrides (e.g. `--c-tooltip-bg`)                                                       | Consumers customizing a component |

### Token Consumption Rules

1. **Semantic first (Default):** Components consume semantic tokens directly. Do not wrap every property in a `--c-*` fallback without need:
   ```css
   @layer molecules {
     .badge {
       background: var(--s-color-brand-primary);
       color: var(--s-color-text-on-brand);
       padding-inline: var(--s-space-compact);
       border-radius: var(--s-radius-full);
     }
   }
   ```
2. **`--c-*` when earned:** Introduce `--c-*` only when a component exposes a public theming API, always with a semantic fallback:
   ```css
   @layer molecules {
     .tooltip {
       background: var(--c-tooltip-bg, var(--s-color-surface-elevated));
       color: var(--c-tooltip-fg, var(--s-color-text-primary));
       border: 1px solid var(--s-color-border-subtle);
       box-shadow: var(--s-shadow-lg);
       padding: var(--s-space-compact);
     }
   }
   ```
3. **Private component variables (`--{component}-*`):** Use internal variables for deduplication or variant switching (variants change the variable, not the property):

   ```css
   @layer molecules {
     .card {
       --card-surface: var(--s-color-surface-elevated);
       background: var(--card-surface);
       padding: var(--s-space-normal);
     }

     .card--highlighted {
       --card-surface: var(--s-color-surface-container);
     }
   }
   ```

4. **Never reference `--p-*` directly in components.**
5. **Zero raw colors:** Never use raw hex, rgb, hsl, or named colors in component CSS. If a semantic token is missing, add it to `shared/tokens.css`.

---

## 2. Mandatory Cascade Layers & Nesting

Declare or participate in flat atomic layers: `@layer atoms, molecules, organisms, pages;`.

- Every rule must be wrapped in a named `@layer` matching the component level:
  ```css
  @layer molecules.select {
    .density-field { ... }
  }
  ```
- No rules outside cascade layers (except standalone reset/reduced-motion overrides).

### Native Nesting Rules

Use native `&` nesting **only** for:

- Pseudo-classes (`:hover`, `:focus-visible`)
- Pseudo-elements (`::before`, `::after`, `::picker-icon`, `::checkmark`)
- Attribute selectors (`[open]`, `[aria-expanded="true"]`)
- State classes (`.is-active`, `.is-disabled`)
- Nested `@media` and `@container` blocks

```css
/* ✅ ALLOWED */
.button {
  background: var(--s-color-brand-primary);

  &:hover {
    background: var(--s-color-brand-primary-hover);
  }

  &:focus-visible {
    outline: 2px solid var(--s-color-brand-accent);
    outline-offset: 2px;
  }
}

/* ❌ FORBIDDEN: String concatenation */
.card {
  &__title { ... } /* NEVER DO THIS */
}

/* ✅ Write BEM elements flat */
.card { ... }
.card__title { ... }
```

---

## 3. Dark Mode & Theming

1. Root declares `color-scheme: light dark;`.
2. Semantic color tokens use `light-dark(lightVal, darkVal)` so theme switching is automatic.
3. Explicit user overrides are supported via `[data-theme="light"]` / `[data-theme="dark"]` and `:root:has(> head > meta[name="color-scheme"][content="..."])`.
4. **Non-color mode adaptations:** `light-dark()` only accepts `<color>`. For mode-dependent non-colors (shadows, opacity, backdrop blur), extract the color into a private variable:
   ```css
   .card {
     --_shadow-color: light-dark(rgba(0, 0, 0, 0.08), rgba(0, 0, 0, 0.5));
     box-shadow: 0 4px 12px var(--_shadow-color);
   }
   ```

---

## 4. Modern Logical Properties & Interaction Standards

1. **Logical properties:** Always use logical properties:
   - `inline-size` / `block-size` instead of `width` / `height`
   - `margin-inline` / `margin-block` instead of `margin-left` / `margin-top`
   - `padding-inline` / `padding-block` instead of `padding-left` / `padding-top`
   - `inset-inline` / `inset-block` instead of `left` / `top`
2. **Individual transforms:** Use `translate`, `rotate`, `scale` instead of `transform: translateX(...)`.
3. **Keyboard Focus:** Always use `:focus-visible` (never bare `:focus`) with visible outline and offset.
4. **Parent-state queries:** Use `:has()` instead of JavaScript state propagation (e.g. `.field:has(input:focus-visible)`).

---

## 5. Design Directions & Taste

### Card & Container Hover Philosophy

- **Hover communicates affordance, not theatrics.**
- **Card containers must NEVER use hover lift transforms** (`translate: 0 -Xpx`, `scale`, or `transform`). They cause visual jitter and feel dated.
- Container hover feedback is achieved through tokenized:
  - `box-shadow`
  - `background-color`
  - `color`
  - `opacity`
- `scale` or transforms are allowed **only on direct child interactive controls** (buttons/links inside cards), never on the card container itself.

```css
/* ✅ Proper container hover feedback */
.card {
  transition:
    box-shadow var(--s-transition-duration-default) var(--s-transition-timing-function-default),
    background-color var(--s-transition-duration-default) var(--s-transition-timing-function-default);

  &:hover {
    box-shadow: var(--s-shadow-md);
    background-color: var(--s-color-surface-hover);
  }

  /* Allowed: child control feedback */
  & > a:hover,
  & > button:hover {
    scale: 1.02;
  }
}
```

### Typography Polish & Fluid Scaling

- **Units — Prefer `rem`:** Always declare `font-size` in `rem` (never `px`) so text respects user zoom and browser preferences. Use `em` only for dimensions directly tied to the local font size (such as icon dimensions or inline badges).
- **Fluid Typography:** Prefer fluid scaling for headings and prominent type using `clamp()` with a `rem` base + dynamic viewport/container units:
  ```css
  /* Fluid heading with rem preservation */
  h1 {
    font-size: clamp(2rem, 1.4rem + 2.5vw, 3.5rem);
  }

  /* Container-query-aware fluid typography */
  .card__title {
    font-size: clamp(1.125rem, 0.95rem + 1cqi, 1.5rem);
  }
  ```
  > [!IMPORTANT]
  > Fluid formulas must **always include a `rem` component** in the preferred value (e.g. `1rem + 2vw`, never `4vw` alone). Without `rem`, the text will not scale when users change their default browser font size.

- Always apply `text-wrap: balance` to headings (`h1` through `h6`).
- Always apply `text-wrap: pretty` to body copy (`p`) and lists (`li`).
- Never use `text-transform: uppercase` without tracking (`letter-spacing: 0.08em`+).

### Responsive Strategy

- Prefer **Container Queries** (`container-type: inline-size`, `@container`) for component-internal responsive reflows.
- Reserve `@media` for viewport-level page shells, print, and user preferences (`prefers-reduced-motion`, `prefers-color-scheme`).
- Fluid grids must use intrinsic sizing (`grid-template-columns: repeat(auto-fill, minmax(min(300px, 100%), 1fr))`).

---

## 6. Self-Check Verification Checklist

Before completing any exercise or feature, verify:

- [ ] `grep -- "--p-" <file>` finds nothing outside `tokens.css`
- [ ] No hex, `rgb()`, `hsl()`, or named color literals in component CSS
- [ ] Every rule is inside a named `@layer`
- [ ] No string-concatenation nesting (`&__child`)
- [ ] Card containers do not use `translate` or `scale` hover lift
- [ ] Headings have `text-wrap: balance` and paragraphs have `text-wrap: pretty`
- [ ] Font sizes use `rem` (never fixed `px`), with fluid `clamp()` where appropriate
- [ ] Logical properties are used (`inline-size`, `padding-inline`, `margin-block`, etc.)
- [ ] Focus states use `:focus-visible` with tokenized outlines
