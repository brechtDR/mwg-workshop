---
name: motion-design
description: Intentional motion design system using modern web platform standards (@starting-style, transition-behavior allow-discrete, interpolate-size, top-layer overlay) and the 3-tier token ladder. Use when animating UI components like popovers, customizable selects, dialogs, reveals, and state transitions.
---

# Motion Design for the Modern Web Platform

This skill defines intentional, accessible motion systems built directly on native web platform capabilities. It adapts the core motion design philosophy of `LottieFiles/motion-design-skill` (MIT) and grounds it in modern CSS standards—such as `@starting-style`, `transition-behavior: allow-discrete`, `interpolate-size`, and the 3-tier design token ladder.

---

## 1. Motion Personality Archetypes

Motion must be intentional, answering *"what changed and why?"* rather than adding decorative flair. Assign **one** motion personality archetype per surface or product context:

| Archetype | Timing | Easing | Amplitude / Distance | Ideal Use Cases |
| :--- | :--- | :--- | :--- | :--- |
| **Calm** | Moderate (`200ms–300ms`) | Soft ease-out, gentle deceleration | Low (2px–6px translate, subtle opacity) | Enterprise tools, reading flows, forms, data grids |
| **Assertive** | Fast (`100ms–200ms`) | Snappy, steep initial acceleration | Minimal (micro-scales, crisp fade) | Developer tools, high-frequency actions, productivity |
| **Expressive** | Generous (`300ms–500ms`) | Gentle spring / overshoot | Moderate (dynamic reveals, morphs) | Onboarding, consumer apps, celebration states |
| **Editorial** | Paced (`400ms–800ms`) | Smooth, cinematic cubic bezier | Hierarchy-sequenced reveals | Marketing landing pages, case studies, narrative storytelling |

> [!IMPORTANT]
> Do not mix personalities arbitrarily within a single flow. A popover and a select within the same interface must share the same motion archetype.

---

## 2. Three-Tier Motion Token Ladder

Motion values follow the project's strict token flow: **Primitives (`--p-*`) → Semantics (`--s-*`) → Component (`--c-*`)**. Never skip tiers or use raw millisecond literals in component CSS.

### Tier 1: Primitives (Defined in `shared/tokens.css`)

```css
@layer tokens {
  :root {
    /* Geometric duration scale */
    --p-duration-100: 100ms;
    --p-duration-150: 150ms;
    --p-duration-200: 200ms;
    --p-duration-300: 300ms;
    --p-duration-500: 500ms;
    --p-duration-800: 800ms;

    /* Platform easing curves */
    --p-ease-standard: cubic-bezier(0.2, 0, 0, 1);
    --p-ease-entrance: cubic-bezier(0, 0, 0.2, 1);
    --p-ease-exit: cubic-bezier(0.4, 0, 1, 1);
    --p-ease-emphasized: cubic-bezier(0.2, 0, 0, 1);
    --p-ease-spring: cubic-bezier(0.16, 1, 0.3, 1);
  }
}
```

### Tier 2: Semantics (Defined in `shared/tokens.css`)

Map context-free primitives to semantic purpose:

```css
@layer tokens {
  :root {
    --s-motion-duration-fast: var(--p-duration-100);
    --s-motion-duration-moderate: var(--p-duration-200);
    --s-motion-duration-deliberate: var(--p-duration-300);

    --s-motion-ease-standard: var(--p-ease-standard);
    --s-motion-ease-entrance: var(--p-ease-entrance);
    --s-motion-ease-exit: var(--p-ease-exit);
    --s-motion-ease-spring: var(--p-ease-spring);
  }
}
```

### Tier 3: Component Overrides (`--c-*`)

Components consume semantic tokens by default and expose `--c-*` tokens only when public theming customization is needed:

```css
@layer molecules.popover {
  .tooltip {
    --_duration: var(--c-tooltip-duration, var(--s-motion-duration-fast));
    --_ease: var(--c-tooltip-ease, var(--s-motion-ease-entrance));
    transition:
      opacity var(--_duration) var(--_ease),
      translate var(--_duration) var(--_ease),
      display var(--_duration),
      overlay var(--_duration);
    transition-behavior: allow-discrete;
  }
}
```

---

## 3. Core Modern Web Platform Standards

Never use JavaScript frame loops or positioning math (`getBoundingClientRect`, Popper/Floating UI) to coordinate animation. Use modern CSS platform primitives:

### A. `@starting-style` for Entry Animations
Elements toggling from `display: none` to visible (e.g. `[popover]`, `<dialog>`, `select::picker`) cannot animate without `@starting-style`. 
- Define the baseline styles the element transitions **from** immediately upon insertion or display change.
- Place `@starting-style` inside or after the open-state selector.

### B. `transition-behavior: allow-discrete`
Allows discrete properties (`display`, `overlay`, `content-visibility`) to participate in CSS transitions so exit animations play smoothly before removal from layout.
> [!CAUTION]
> Always declare `transition-behavior: allow-discrete` on its own line **after** the `transition` shorthand. Including `allow-discrete` inside the shorthand string will cause older browsers to drop the entire transition declaration.

### C. The `overlay` Property for Top-Layer Promotion
Elements entering or leaving the browser's top layer (`[popover]`, `<dialog>`) must include `overlay` in their `transition-property` list. This prevents the element from being clipped or dropped out of the top layer until its exit transition finishes.

### D. `interpolate-size: allow-keywords`
Enables smooth transitions between fixed metric dimensions and intrinsic sizing keywords (`height: 0` to `height: auto`, or `block-size: 0` to `block-size: auto`).
- Declare globally on `:root` or on the animated container:
```css
:root {
  interpolate-size: allow-keywords;
}
```

---

## 4. Component Motion Recipes

### Recipe 1: Anchored Popover / Tooltip Popping

Smoothly animates an anchored popover or tooltip popping into view and fading/translating out when dismissed.

```css
@layer molecules.popover {
  /* Closed / Base exit state */
  [popover] {
    opacity: 0;
    translate: 0 4px;
    scale: 0.98;

    /* MANDATORY: transition display and overlay alongside visual properties */
    transition:
      opacity var(--s-motion-duration-fast) var(--s-motion-ease-exit),
      translate var(--s-motion-duration-fast) var(--s-motion-ease-exit),
      scale var(--s-motion-duration-fast) var(--s-motion-ease-exit),
      display var(--s-motion-duration-fast),
      overlay var(--s-motion-duration-fast);
    transition-behavior: allow-discrete;
  }

  /* Open state */
  [popover]:popover-open {
    opacity: 1;
    translate: 0 0;
    scale: 1;
    transition:
      opacity var(--s-motion-duration-moderate) var(--s-motion-ease-entrance),
      translate var(--s-motion-duration-moderate) var(--s-motion-ease-entrance),
      scale var(--s-motion-duration-moderate) var(--s-motion-ease-entrance),
      display var(--s-motion-duration-moderate),
      overlay var(--s-motion-duration-moderate);
    transition-behavior: allow-discrete;

    /* Entry baseline: where it begins animating FROM */
    @starting-style {
      opacity: 0;
      translate: 0 4px;
      scale: 0.98;
    }
  }

  /* Backdrop animation (if modal popover) */
  [popover]::backdrop {
    background-color: rgb(0 0 0 / 0);
    transition:
      background-color var(--s-motion-duration-moderate) var(--s-motion-ease-standard),
      display var(--s-motion-duration-moderate),
      overlay var(--s-motion-duration-moderate);
    transition-behavior: allow-discrete;
  }

  [popover]:popover-open::backdrop {
    background-color: light-dark(rgb(14 15 15 / 0.2), rgb(0 0 0 / 0.6));

    @starting-style {
      background-color: rgb(0 0 0 / 0);
    }
  }
}
```

### Recipe 2: Customizable Select (`appearance: base-select`)

Animates the dropdown picker container unfolding or fading in, alongside smooth icon rotation on the picker chevron.

```css
@layer molecules.select {
  /* Ensure keyword interpolation is enabled */
  :root {
    interpolate-size: allow-keywords;
  }

  /* Opt in to customizable select */
  .custom-select,
  .custom-select::picker(select) {
    appearance: base-select;
  }

  /* Chevron rotation on open */
  .custom-select::picker-icon {
    transition: rotate var(--s-motion-duration-moderate) var(--s-motion-ease-spring);
  }

  .custom-select:open::picker-icon {
    rotate: 180deg;
  }

  /* Picker dropdown container (Closed / Base state) */
  .custom-select::picker(select) {
    opacity: 0;
    block-size: 0;
    translate: 0 -4px;

    transition:
      opacity var(--s-motion-duration-fast) var(--s-motion-ease-exit),
      block-size var(--s-motion-duration-moderate) var(--s-motion-ease-spring),
      translate var(--s-motion-duration-fast) var(--s-motion-ease-exit),
      display var(--s-motion-duration-moderate),
      overlay var(--s-motion-duration-moderate);
    transition-behavior: allow-discrete;
  }

  /* Picker dropdown container (Open state) */
  .custom-select:open::picker(select) {
    opacity: 1;
    block-size: auto;
    translate: 0 0;

    transition:
      opacity var(--s-motion-duration-moderate) var(--s-motion-ease-entrance),
      block-size var(--s-motion-duration-moderate) var(--s-motion-ease-spring),
      translate var(--s-motion-duration-moderate) var(--s-motion-ease-entrance),
      display var(--s-motion-duration-moderate),
      overlay var(--s-motion-duration-moderate);
    transition-behavior: allow-discrete;

    /* Entry baseline */
    @starting-style {
      opacity: 0;
      block-size: 0;
      translate: 0 -4px;
    }
  }
}
```

> [!TIP]
> Never set `overflow: hidden` on `::picker(select)`. The picker must remain scrollable so options can be reached when the viewport is constrained or zoomed.

---

## 5. Choreography & Performance Rules

1. **Keep motion on the compositor thread:**
   - Prefer animating `opacity`, `translate`, `scale`, and `rotate`.
   - Never animate `top`, `left`, `bottom`, `right`, `margin`, or physical geometry for positioning.
2. **Single dominant actor:**
   - In any given interaction (e.g. opening a select or popover), only one primary element should command spatial attention. Secondary indicators (like the picker arrow) should complement, not compete.
3. **Hierarchy over decoration:**
   - Staggering should reveal reading order or visual priority (e.g., label → items), never just random delays. Keep stagger intervals tiny (`20ms–40ms`).
4. **Card & container hover taste rule:**
   - **Never apply hover lift transforms** (`translate: 0 -Xpx`, `scale`) to card containers. Affordance is communicated through tokenized `box-shadow`, `background-color`, and `border-color`.
   - Small scales (`1.02`) are permitted only on direct interactive child controls (buttons/links inside the container).

---

## 6. Reduced Motion & Accessibility (WCAG 2.2)

Every motion pattern must respect user preferences. Never apply a global `* { transition: none !important; }` that causes visual breakage or traps elements in mid-state.

### Strategy A: Opt-in to spatial movement
Apply spatial translations, scale, and springs only when `prefers-reduced-motion: no-preference` is satisfied:

```css
@layer molecules.popover {
  @media (prefers-reduced-motion: no-preference) {
    [popover]:popover-open {
      translate: 0 0;
      scale: 1;

      @starting-style {
        translate: 0 4px;
        scale: 0.98;
      }
    }
  }
}
```

### Strategy B: Graceful duration collapse
For users requesting reduced motion, preserve immediate opacity/fade feedback while eliminating motion sickness triggers:

```css
@layer molecules.popover {
  @media (prefers-reduced-motion: reduce) {
    [popover],
    [popover]:popover-open,
    .custom-select::picker(select) {
      translate: none !important;
      scale: none !important;
      transition-duration: 50ms !important;
    }

    .custom-select::picker-icon {
      transition: none !important;
    }
  }
}
```

### Safety Requirements
- **Flash threshold:** No content may flash more than 3 times in any 1-second period (WCAG 2.3.1).
- **Persistent motion pause:** Any continuous motion lasting longer than 5 seconds must provide an accessible pause/stop control (WCAG 2.2.2).
- **Final state guarantee:** Disabled animations must resolve to their final visible state (`opacity: 1`, visible focus rings).

---

## 7. Motion Quality Verification Checklist

Before considering an animated component complete, verify:

- [ ] Motion tokens come from the 3-tier token ladder (`--s-motion-*`), with no hardcoded millisecond numbers in component rules.
- [ ] `@starting-style` is defined on or after the `:open` / `:popover-open` state.
- [ ] `overlay` and `display` are transitioned whenever moving into or out of the top layer.
- [ ] `transition-behavior: allow-discrete` is declared separately, directly after the `transition` property.
- [ ] Auto-sizing transitions utilize `interpolate-size: allow-keywords` (on `:root` or container).
- [ ] Individual transform properties (`translate`, `scale`, `rotate`) are used instead of `transform: ...`.
- [ ] Spatial animations are disabled or simplified under `@media (prefers-reduced-motion: reduce)`.
- [ ] `::picker(select)` preserves scrollability (no `overflow: hidden`).
- [ ] Card containers do not use `translate` or `scale` hover lifts.
- [ ] All rules reside inside designated `@layer` blocks.
