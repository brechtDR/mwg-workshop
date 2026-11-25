# Modern Web Guidance: Workshop Exercises

Starter exercises and agent guidance rules for the **Modern Web Guidance** workshop.

This repository is designed as a template for workshop attendees to build their own solutions using modern, standards-first web platform APIs and AI pair programming.

---

## Quickstart

### 1. Create Your Workshop Workspace

Click the green **[Use this template](https://github.com/brechtDR/mwg-workshop/generate)** button at the top of this repository (or click **Fork**) to create your own copy on your GitHub account.

### 2. Clone and Start

```bash
git clone https://github.com/<your-username>/mwg-workshop.git
cd mwg-workshop
npm start                # serves locally at http://localhost:8081
```

Open the `mwg-workshop` folder as your workspace in your editor (such as Antigravity).

### 3. Verify Modern Web Guidance

Verify that the Modern Web Guidance CLI is available:

```bash
npx -y modern-web-guidance@latest search "popover"
```

If this returns structured JSON guidance for the Popover API, you're all set!

---

## Exercise Catalog

Each folder is a self-contained exercise designed to explore a modern web platform capability without external UI libraries:

| Exercise | Focus Area | Key Platform Features |
| :--- | :--- | :--- |
| **`01-popover`** | Anchored Tooltips | `popover`, `popovertarget`, CSS Anchor Positioning (`anchor()`, `position-anchor`) |
| **`02-select`** | Stylable Native Select | Stylable `<select>`, `::picker(select)`, `<selectedcontent>` |
| **`03-scroll`** | Declarative Animations | Scroll-driven animations (`animation-timeline: scroll()`, `view()`, `animation-range`) |
| **`04-density`** | Fluid Layout & Density | CSS Container Queries (`@container`), fluid spacing ladder |
| **`05-carousel`** | Dependency-Free Carousel | CSS Scroll Snap, `scroll-target-group`, `@container scroll-state()` |
| **`06-swipe`** | Swipe-to-Delete Action | Native scroll snap paging, spacer snap targets, focus projection |
| **`07-design-md`** | Design Contract Implementation | Implementing token ladders against a markdown design contract (`DESIGN.md`) |

---

## Architecture & Design Tokens

Shared styling lives in `shared/`:
- **`shared/tokens.css`**: The 3-tier token ladder (`--p-*` primitive tokens $\to$ `--s-*` semantic tokens $\to$ `--c-*` component tokens).
- **`shared/base.css`**: Baseline reset, cascade layers, layout shells, and dark mode handling.

---

## AI Agent Kit

This workspace includes agent configuration in `.agents/`:
- **Rules (`.agents/rules/`)**: Enforces platform-first implementation and token ladder discipline.
- **Skills (`.agents/skills/token-ladder/`)**: Guidance on styling components using cascade layers and token architecture.

When prompting an AI assistant in this repository, the agent will automatically adhere to modern web platform APIs rather than defaulting to outdated patterns or unnecessary dependencies.

---

## Note for Attendees

> [!NOTE]
> This repository is maintained as a template starter kit. Issues are disabled and Pull Requests are restricted to maintainers. Please build, commit, and experiment directly inside your personal template clone or fork!
