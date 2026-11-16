---
description: Summary of the reference course page on the folder structure of a Sass design system (six sNN layers, their contents, load order and the utilities import), which Zanshin's src/styles/ follows. Read when you need to know what the reference structure prescribes and where Zanshin deviates from it.
---

# Source: design-system reference, folder structure

What it is: page 17 of the reference course series on design systems (a DAW/DIW course, CC BY-SA 4.0), based on the Sass Guidelines 7-1 architecture. It is the reference structure for `src/styles/` in Zanshin. Page last modified 2026-10-30; filed in [raw/design-system-reference-folder-structure.md](raw/design-system-reference-folder-structure.md) on 2026-11-14. The sibling pages are filed as [design-system-bem.md](design-system-bem.md), [design-system-tokens.md](design-system-tokens.md), [design-system-globals.md](design-system-globals.md) and [design-system-layout.md](design-system-layout.md).

Statements that matter for the project:

1. Styles live in `src/styles/` in six folders, named `s` plus a two-digit number: `s01_utilities`, `s02_vendor`, `s03_base`, `s04_global`, `s05_components`, `s06_layouts`. The number fixes the cascade order; the `s` exists because a Sass namespace cannot start with a digit.
2. All six folders always exist, even when one holds only an empty `_index.scss`, so every project has the same structure.
3. `s01_utilities` holds `_variables.scss`, `_functions.scss` and `_mixins.scss`; it emits no CSS and is not loaded from `styles.scss`.
4. `s02_vendor` holds third-party CSS, each library in its own subfolder; its `_index.scss` is empty when there is none.
5. `s03_base` holds `_system-tokens.scss` (system tokens on `:root`, generated with loops from the variables), `_reset.scss` and `_typography.scss`.
6. `s04_global` holds one file per global modifier type (`_g--color.scss`, `_g--font-size.scss`, `_g--margin.scss`, `_g--padding.scss`). Responsive variants go in the same file, after the base classes, generated from `$breakpoints`.
7. `s05_components` holds one `c-` block per file; `s06_layouts` holds one `l-` block per file. Components come before layouts in the cascade.
8. Each `_index.scss` only `@forward`s its folder's files. `styles.scss` only `@use`s `s02` to `s06` in order, before any other rule.
9. A file that needs variables, functions or mixins adds `@use "../s01_utilities/index" as utilidades;`.
10. In Angular, a component or layout is built one of two ways, never both: a CSS-only block in `s05`/`s06`, or an Angular component with its own `.scss`.

Pages changed by this source: [design-system.md](design-system.md), [architecture.md](architecture.md#file-organization), [technology.md](technology.md#development-tools), [implementation-plan.md](implementation-plan.md#stage-0-project-setup).

Where Zanshin deviates, and why, is listed in [design-system.md](design-system.md#deviations-from-the-reference).
