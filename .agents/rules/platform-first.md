---
description: Look up how the web platform solves a UI pattern and apply modern CSS standards before writing any HTML, CSS, or client-side JavaScript.
always_on: true
---

# Platform First & Modern CSS Standards

## 1. Modern Web Platform First
1. Before writing UI code, search Modern Web Guidance for the pattern and retrieve the top guide:
   ```bash
   npx -y modern-web-guidance@latest search "<pattern>"
   ```
2. Always use native platform primitives (Popover API, anchor positioning, `<dialog>`, customizable select with `appearance: base-select`, scroll-driven animations, container queries) instead of rebuilding them in JavaScript.
3. **No JavaScript positioning math:** no `getBoundingClientRect()`, no manual coordinate calculation, no scroll/resize placement listeners, no Popper/Floating UI.
4. Gate newer CSS behind `@supports` so unsupported browsers gracefully degrade to native controls.
5. Styling decisions come from the `token-ladder` skill and project design contracts, not from guidance starter examples.

## 2. Modern CSS & Interaction Standards
- **Native Nesting Rules:**
  - Use native `&` nesting **only** for:
    - pseudo-classes (`:hover`, `:focus-visible`)
    - pseudo-elements (`::before`, `::after`, `::picker-icon`, `::checkmark`)
    - attribute selectors (`[open]`, `[aria-expanded="true"]`)
    - state classes (`.is-active`, `.is-disabled`)
    - nested `@media` and `@container` blocks
  - **Forbidden nesting pattern:** Never use string concatenation nesting (`&__title` or `&--modifier`). Write BEM child elements and modifiers flat.
- **Logical Properties:**
  - Always prefer logical properties (`inline-size`, `block-size`, `margin-inline`, `padding-block`, `inset-inline`, `inset-block`) over physical properties (`width`, `height`, `margin-left`, `top`).
- **Individual Transforms:**
  - Use individual transform properties (`translate: 0 0`, `rotate: 0deg`, `scale: 1`) instead of combined `transform: translate(...) rotate(...)`.
- **Focus & Parent State:**
  - Use `:focus-visible` (never bare `:focus`) with tokenized outline and offset.
  - Use `:has()` for parent-state styling (e.g. `.field:has(input:focus-visible)`).
- **Responsive Architecture:**
  - Prefer **Container Queries** (`container-type: inline-size`, `@container`) for component-internal responsiveness.
  - Reserve `@media` for viewport-level shell layouts, print, and user preference queries (`prefers-reduced-motion`, `prefers-color-scheme`).
  - Fluid grids must use intrinsic sizing (`grid-template-columns: repeat(auto-fill, minmax(min(300px, 100%), 1fr))`), avoiding rigid breakpoint-based column counts.
- **Typography & Units:**
  - **Prefer `rem` for all font sizes** (never fixed `px`) so text respects user zoom and browser default font preferences. Use `em` only when dimensions must scale proportionally to the element's local font size.
  - **Fluid Typography:** Prefer fluid typography using `clamp()` combining a `rem` base with dynamic units (viewport `vw` or container `cqi`), e.g. `clamp(1.75rem, 1.25rem + 2vw, 2.75rem)`. Never use raw viewport units alone without a `rem` base, as this breaks browser font resizing.
  - Always apply `text-wrap: balance` to headings and `text-wrap: pretty` to body text and lists.
  - Never apply `text-transform: uppercase` without tracking (`letter-spacing: 0.08em`+).
- **Motion:**
  - State enter transitions should leverage `@starting-style` with `transition-behavior: allow-discrete`.
  - Elements entering/exiting the top layer must transition `overlay` and `display`.
