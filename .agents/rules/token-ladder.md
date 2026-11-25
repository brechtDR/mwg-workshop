---
description: Non-negotiable token, cascade-layer, and interaction constraints for every exercise. Always in context so no CSS bypasses the design system.
always_on: true
---

# Token Ladder & CSS Guard

## 1. Strict Token Hierarchy (Never Skip Levels)
- Tokens flow in one direction: `--p-*` (primitives) -> `--s-*` (semantic) -> `--c-*` (component overrides).
- **Components must never reference `--p-*` directly.** Primitives hold context-free values only.
- **Semantic first:** Components consume `--s-*` directly by default. Do not wrap every property in `--c-*`.
- **`--c-*` when earned:** Introduce `--c-*` only when an explicit public override API is intended, always with a semantic fallback: `var(--c-button-bg, var(--s-color-brand-primary))`.
- **Private component variables:** Use `--{component}-{property}` (e.g. `--card-surface`) for internal repeated values or variant switching (variants change the private variable, not the property).
- **Zero raw colors:** No hex, rgb, hsl, oklch, or named color literals in component CSS. Missing semantic tokens must be added to `shared/tokens.css`.

## 2. Mandatory Cascade Layers
- Every CSS rule must live inside a named `@layer` (`atoms`, `molecules`, `organisms`, `pages`), e.g. `@layer molecules.tooltip { ... }` or `@layer molecules { ... }`.
- Layer naming must match the component's atomic level.
- No CSS rules may exist outside cascade layers (except standalone global reset/reduced-motion overrides).

## 3. Dark Mode & Theming
- Root must declare `color-scheme: light dark;`.
- Semantic color tokens must use `light-dark(lightVal, darkVal)`.
- Support explicit user overrides via `[data-theme="light"]` / `[data-theme="dark"]` (and `:root:has(> head > meta[name="color-scheme"][content="..."])`).
- **Non-color mode variations:** `light-dark()` only accepts `<color>`. For mode-dependent non-colors (shadows, blur, opacity), extract color into a private variable (e.g. `--_shadow-color: light-dark(...); box-shadow: 0 4px 12px var(--_shadow-color);`).

## 4. Interaction & Hover Discipline
- **Hover communicates affordance, not theatrics.**
- **Card containers must NEVER use hover lift transforms** (`translate: 0 -Xpx`, `scale`, or `transform`). They cause visual jitter and feel dated.
- Container hover feedback must use tokenized `box-shadow`, `background-color`, `color`, or `opacity`.
- `scale` or transforms are allowed only on child interactive elements inside containers (buttons/links), never on the container itself.
- All transitions must use tokenized durations/easings, never hardcoded timings like `0.2s`.
