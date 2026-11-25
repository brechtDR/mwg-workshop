---
version: alpha
name: Defying Gravity Editorial
description: A warm editorial system for a front-end publication. Playfair Display headlines over DM Sans body copy, a magenta brand accent used sparingly, and warm neutral canvases. Quiet surfaces, loud type.

colors:
  primary: "oklch(58% 0.22 355)"
  primary-dark: "oklch(72% 0.2 355)"
  accent: "oklch(55% 0.2 250)"
  accent-dark: "oklch(75% 0.18 250)"
  highlight: "oklch(68% 0.18 65)"
  highlight-dark: "oklch(82% 0.15 65)"
  neutral-0: "oklch(100% 0 0)"
  neutral-50: "oklch(98% 0.005 60)"
  neutral-100: "oklch(94% 0.01 60)"
  neutral-200: "oklch(88% 0.015 60)"
  neutral-400: "oklch(62% 0.018 60)"
  neutral-700: "oklch(35% 0.02 60)"
  neutral-800: "oklch(22% 0.02 60)"
  neutral-900: "oklch(14% 0.02 60)"
  neutral-950: "oklch(8% 0.01 60)"

typography:
  headline-display:
    fontFamily: "Playfair Display, Georgia, Cambria, serif"
    fontSize: 3.5rem
    fontWeight: 700
    lineHeight: 1.05
    letterSpacing: -0.02em
  headline-md:
    fontFamily: "Playfair Display, Georgia, Cambria, serif"
    fontSize: 2rem
    fontWeight: 600
    lineHeight: 1.15
  body-lg:
    fontFamily: "DM Sans, -apple-system, BlinkMacSystemFont, Segoe UI, sans-serif"
    fontSize: 1.25rem
    fontWeight: 400
    lineHeight: 1.55
  body-md:
    fontFamily: "DM Sans, -apple-system, BlinkMacSystemFont, Segoe UI, sans-serif"
    fontSize: 1rem
    fontWeight: 400
    lineHeight: 1.6
  label-caps:
    fontFamily: "JetBrains Mono, ui-monospace, Menlo, monospace"
    fontSize: 0.75rem
    fontWeight: 500
    lineHeight: 1
    letterSpacing: 0.12em

rounded:
  sm: 6px
  md: 10px
  lg: 16px
  full: 9999px

spacing:
  "1": 4px
  "2": 8px
  "3": 12px
  "4": 16px
  "6": 24px
  "8": 32px
  "12": 48px

components:
  button-primary:
    backgroundColor: "{colors.primary}"
    textColor: "{colors.neutral-0}"
    typography: "{typography.body-md}"
    rounded: "{rounded.full}"
    padding: 12px 24px
  button-primary-hover:
    backgroundColor: "{colors.neutral-900}"
  button-primary-dark:
    backgroundColor: "{colors.primary-dark}"
    textColor: "{colors.neutral-950}"
  eyebrow:
    textColor: "{colors.accent}"
    typography: "{typography.label-caps}"
  eyebrow-dark:
    textColor: "{colors.accent-dark}"
  badge:
    backgroundColor: "{colors.highlight}"
    textColor: "{colors.neutral-950}"
    typography: "{typography.label-caps}"
    rounded: "{rounded.full}"
  badge-dark:
    backgroundColor: "{colors.highlight-dark}"
    textColor: "{colors.neutral-950}"
  hero:
    backgroundColor: "{colors.neutral-50}"
    textColor: "{colors.neutral-900}"
  hero-dark:
    backgroundColor: "{colors.neutral-950}"
    textColor: "{colors.neutral-50}"
  card:
    backgroundColor: "{colors.neutral-0}"
    textColor: "{colors.neutral-700}"
    rounded: "{rounded.md}"
    padding: 24px
  card-dark:
    backgroundColor: "{colors.neutral-800}"
    textColor: "{colors.neutral-200}"
  divider:
    backgroundColor: "{colors.neutral-400}"
    height: 1px
  media-frame:
    backgroundColor: "{colors.neutral-100}"
    rounded: "{rounded.lg}"
---

# Defying Gravity Editorial

The visual contract for every page in this workshop. Agents: read this file
before writing any UI, and map every value onto the 3-tier token ladder in
`shared/tokens.css` — never paste a raw value into a component.

## Overview

An editorial publication about the modern web platform. It should feel like a
well-set magazine, not a SaaS dashboard: confident serif headlines, generous
whitespace, and one loud colour used with restraint. The audience is
front-end developers — they notice sloppy spacing and generic defaults.

## Colors

- **Magenta** (`{colors.primary}`): the brand accent. Reserved for the single
  most important action on a screen and for short, high-impact highlights.
  In dark mode use `{colors.primary-dark}`.
- **Azure** (`{colors.accent}`): eyebrows, links, and secondary emphasis.
  Never as a large background.
- **Amber** (`{colors.highlight}`): badges and "new" markers only.
- **Warm neutrals** (`neutral-*`): all surfaces and text. Surfaces sit on
  `{colors.neutral-50}`, elevated cards on `{colors.neutral-0}`, body text on
  `{colors.neutral-900}`.

Every colour pairing must meet WCAG 2.2 AA (4.5:1 for body text, 3:1 for
large text and UI boundaries).

## Typography

- **Headlines:** Playfair Display, bold, tight tracking. Sentence case.
  Balance headline wrapping (`text-wrap: balance`).
- **Body:** DM Sans at `{typography.body-md}`; lead paragraphs at
  `{typography.body-lg}`, capped around 60 characters per line.
- **Labels:** JetBrains Mono uppercase (`{typography.label-caps}`) for
  eyebrows, metadata, and badges. Never for sentences.

## Layout

A 4px base scale (`spacing.1` to `spacing.12`). Sections breathe:
`{spacing.12}` between blocks, `{spacing.6}` inside cards. Heroes use a
two-column grid that collapses to one column based on the space the
component has (container queries), not the viewport.

## Elevation & Depth

Depth comes from tonal layers — `neutral-50` canvas, `neutral-0` cards —
and thin `neutral-200` borders. Shadows are subtle and re-tuned per colour
scheme; no heavy drop shadows, no glassmorphism.

## Shapes

Soft but not bubbly: `{rounded.md}` for cards and inputs, `{rounded.lg}` for
media frames, `{rounded.full}` for buttons and badges only.

## Components

- **Primary button:** pill (`{rounded.full}`), magenta background, white
  label. Hover darkens to `neutral-900`; no scale or lift animation. One per
  view.
- **Eyebrow:** mono caps label in azure above a headline.
- **Hero:** eyebrow, display headline, lead paragraph, one primary CTA, and
  a media frame. Headline and CTA copy are direct and specific.

## Do's and Don'ts

- Do map every token to the ladder: DESIGN.md values become `--p-*`
  primitives, purpose lives in `--s-*`, overrides in `--c-*`.
- Do put every rule inside a named `@layer`.
- Do keep a visible focus ring on every interactive element.
- Do respect `prefers-reduced-motion`.
- Don't use system blue (`#0066ff`, `#007bff`, `#2563eb`) or Inter as a
  default.
- Don't add a generic gray-shadow "lift" on card hover.
- Don't use more than one primary button per view.
- Don't write marketing filler ("Unlock the power of…", "Revolutionize…").
