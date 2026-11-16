---
description: The styling system in one place: Sass layers, system and semantic variables and tokens, per-block design tokens and private state, BEM naming, global modifiers, layout blocks, Stylelint rules and the visual direction. Read when you write or review any SCSS, add a c-/l-/g-- block or class, or change a colour, size or theme.
---

# Design system

What belongs here: how styles are organized and how a value travels from a raw Sass map to a CSS declaration. The structure and rules follow a reference course on design systems, filed as five sources: [folder structure](design-system-folder-structure.md), [BEM](design-system-bem.md), [tokens](design-system-tokens.md), [global modifiers](design-system-globals.md) and [layout blocks](design-system-layout.md). [architecture.md](architecture.md) and [technology.md](technology.md) stay the source of truth for file layout and tooling; this page links to them.

## Principles

- **One global Dart Sass project**, no Angular component styles. Angular components only apply classes; a component and its BEM block share a name (`zn-kanban-card` → `.c-kanban-card`). See [architecture.md](architecture.md#architectural-decisions).
- **Module system only**: `@use` / `@forward` with namespaces, built-in modules such as `sass:map` and `sass:list`; no `@import`, no global built-in functions. See [technology.md](technology.md#frameworks-and-libraries).
- **No UI library** (no Material components, Bootstrap or Tailwind). Every visual is built from the project's own tokens and blocks.
- **Nothing typed by hand that a loop can generate**: system tokens, semantic tokens and global modifiers all come from the Sass maps.
- **Visual direction**: calm and restrained, inspired by kyūdō. Off-white paper surfaces, ink-dark text, one vermilion accent, generous spacing, subtle motion, and a quiet accent per column tied to its stage of the shot (see the column table in [product.md](product.md#purpose)).

## Layers

Styles live in `src/styles/` in six numbered folders. The number fixes the cascade order; the `s` exists because a Sass namespace cannot start with a digit. All six folders always exist, even when one holds only an empty `_index.scss`. A lower layer may be used by a higher one, never the reverse.

| Folder | Holds | Emits CSS |
|--------|-------|-----------|
| `s01_utilities` | Sass maps (`_system-variables.scss`, `_semantic-variables.scss`), `_functions.scss` and `_mixins.scss` (breakpoints, focus ring, visually-hidden, reduced motion) | No |
| `s02_vendor` | Third-party CSS, one subfolder per library. Empty in Zanshin: there is no UI library | Yes |
| `s03_base` | `--zn-sys-*` and `--zn-sem-*` custom properties on `:root`, reset, element typography and fonts | Yes |
| `s04_global` | `g--*` global modifier classes, one file per type (`_g--color.scss`, `_g--font-size.scss`, `_g--margin.scss`, `_g--padding.scss`), with their responsive variants after the base classes | Yes |
| `s05_components` | `c-*` blocks with a visual identity (button, header, column, card, dialog, field, notice) | Yes |
| `s06_layouts` | `l-*` blocks that place things (`l-app`, `l-board`, `l-stack`) and have no visual identity | Yes |

- Each `_index.scss` only `@forward`s the partials of its folder.
- `styles.scss` only `@use`s `s02_vendor`, `s03_base`, `s04_global`, `s05_components` and `s06_layouts`, in that order, before any other rule. `s01_utilities` is not loaded there because it emits no CSS.
- A partial that needs variables, functions or mixins adds `@use '../s01_utilities' as utilities;` at the top.

The full tree is in [architecture.md](architecture.md#file-organization).

Access rules between layers:

- `s03_base` token partials and `s04_global` may read `s01_utilities` variables (tokens and globals are generated from the maps).
- `s05_components` and `s06_layouts` may use `s01_utilities` **mixins and functions only**, never its variables. They read values at runtime through `var(--zn-sem-…)` / `var(--zn-sys-…)`.

## Value flow

A value passes through these steps. Steps 1 and 2 exist only at compile time; the rest are custom properties in the browser.

1. **System variables** (`s01_utilities/_system-variables.scss`): raw values as plural Sass maps by category, keys in kebab-case: `$colors`, `$font-families`, `$font-sizes`, `$font-weights`, `$line-heights`, `$paddings`, `$margins`, `$radii`, `$shadows`, `$durations`, `$easings`, `$breakpoints`. Single values use `$kebab-case`.
2. **Semantic variables** (`s01_utilities/_semantic-variables.scss`): maps prefixed `$semantic-` whose values are keys of a system map, giving roles to raw values: `$semantic-colors: ('surface': 'paper-50', 'accent': 'vermilion-500', …)`. Roles include surface, surface-raised, on-surface, muted, accent, on-accent, border, focus-ring, danger, and one per column.
3. **System tokens** (`s03_base/_system-tokens.scss`): `--zn-sys-<category>-<step>` on `:root`, generated with `@each` / `@for` from the system variables (`--zn-sys-color-vermilion-500`, `--zn-sys-font-size-3`, `--zn-sys-padding-md`). The full palette and every scale step.
4. **Semantic tokens** (`s03_base/_semantic-tokens.scss`): `--zn-sem-<category>-<role>` on `:root`, each a `var()` of a system token (`--zn-sem-color-surface: var(--zn-sys-color-paper-50)`, `--zn-sem-color-column-draw`). Blocks use semantic colours, never the palette: a block is `accent`, not `vermilion`.
5. **Design tokens** (`--<block>-<property>[-<variant>]`): declared at the top of each `c-`/`l-` block, using the block name without its prefix. Public: a designer's decision, **one single value each**, by default a semantic or system token. Three colours means three tokens (`--button-color`, `--button-color-danger`), never one token with three values.
6. **Private state** (`--<block>-privateState-<property>`): declared after the design tokens. Not a token but the CSS needed to implement the block; its value depends on the modifiers the block carries. Only the block's own modifiers (and pseudo-classes such as `:hover`) change it. The `privateState` segment keeps its camelCase and custom property names are case-sensitive.
7. **CSS properties**: only read the block's private state and design tokens.

A block with no modifiers skips step 6: each property reads its design token directly. The moment a block has a modifier, the properties the modifier affects go through private state.

```scss
// s05_components/_c-button.scss
.c-button {
  // Design tokens: one value each, public
  --button-color: var(--zn-sem-color-accent);
  --button-on-color: var(--zn-sem-color-on-accent);
  --button-font-size: var(--zn-sys-font-size-3);
  --button-padding-vertical: var(--zn-sys-padding-sm);
  --button-padding-horizontal: var(--zn-sys-padding-md);

  // Private state: defaults for the solid button; only modifiers change it
  --button-privateState-background: var(--button-color);
  --button-privateState-text-color: var(--button-on-color);

  // Properties: only state and tokens
  font-size: var(--button-font-size);
  padding: var(--button-padding-vertical) var(--button-padding-horizontal);
  background: var(--button-privateState-background);
  color: var(--button-privateState-text-color);

  // Modifiers change state, never tokens and never properties
  &--ghost {
    --button-privateState-background: transparent;
    --button-privateState-text-color: var(--button-color);
  }
}
```

Rules:

- Design tokens may only reference semantic tokens, system tokens, another design token of the same block, or a keyword such as `transparent`. Never Sass variables, never literal values.
- Colours always go through semantic tokens (`--zn-sem-color-*`). Scale steps that carry no role (font sizes, paddings, radii, durations) may use system tokens directly.
- Private state may only reference the block's design tokens or other private state of the same block.
- Style declarations may only use the block's own private state and design tokens.
- Modifiers and pseudo-classes **always** change private state, never a CSS property and never a design token. Each modifier group owns its own state variables, so modifiers combine freely. Example: `.c-kanban-column--quiver` only sets `--kanban-column-privateState-accent` to `var(--kanban-column-accent-quiver)`.
- A pseudo-class cannot know which modifier the block carries: the modifier leaves the value the pseudo-class needs ready in state (`--button-privateState-color-hover`), and `:hover` only copies it.
- To customise a block in a context, a parent redefines the block's **design tokens** (`.c-dialog .c-button { --button-color: var(--zn-sem-color-danger); }`) and only those the block documents as public. Never redefine another block's private state.
- A `c-` block never sets its own outer position (margins, grid placement); that is the job of an `l-` block, a parent element or a `g--` class.

*Why:* raw values change in one place; semantic tokens give one place to retheme the app; design tokens make each block's adjustable surface explicit; private state keeps modifiers small and independent. A dark theme is a remap of `--zn-sem-*` inside a theme selector or media query (planned for v1.1 in [roadmap.md](roadmap.md#v11-comfort)).

## Naming (BEM)

- Block prefixes: `c-` for components, `l-` for layouts, `g--` for global modifiers. No other prefixes exist.
- Element: block + `__` + element (`.c-kanban-card__title`); the `__` is required. Modifier: block or element + `--` + modifier (`.c-kanban-card--dragging`, `.l-stack--tight`).
- Each tag carries at most one block class or one element class. An element's tag is always inside its block's tag. Any other classes on the tag are modifiers of that same block or element, or `g--` classes.
- Content inside a block that is not part of it is another block, not an element.
- One block per partial: `_c-<name>.scss` in `s05_components`, `_l-<name>.scss` in `s06_layouts`, `_g--<type>.scss` in `s04_global`. The partial holds a single `.c-<name> { }` with every element, modifier and pseudo-class nested with `&`, so the block name is written once.
- No nesting deeper than the element level; no styling by element type or ID outside `s03_base`.
- Name what the thing is, not its content: a class must still make sense when the text changes (`c-kanban-card__title`, not `c-kanban-card__task-name`).
- If two elements share almost all their CSS, they are one element with a modifier that changes only the difference.
- CDK drag-drop preview and placeholder elements get `c-kanban-card` modifier classes, because there are no component-scoped styles.

The full naming list is in [architecture.md](architecture.md#naming-conventions).

## Global modifiers

A global modifier (`g--`) is a class usable on any element that changes **one property only**, from a system token, with `!important`. It replaces an inline `style`, which cannot be responsive.

- Pattern: `g--<property>-<step>`, with `-<side>-`, `-horizontal-` and `-vertical-` variants for margins and paddings; the value is `var(--zn-sys-<property>-<step>)`.
- Files in `s04_global`, one per type: `_g--color.scss` (`color`, `background-color`, `border-color` from the system colour tokens), `_g--font-size.scss`, `_g--margin.scss`, `_g--padding.scss`. Generated with `@each` over the same maps as the system tokens, so class and token always match.
- Responsive variants: the same class with a `@<breakpoint>` suffix, generated from `$breakpoints` inside `min-width` media queries, in the same file and **after** the base classes (the base already carries `!important`; placed before, it wins and the variant does nothing). The `@` is escaped as `\@` in the selector, not in the template: `class="g--padding-sm g--padding-lg@desktop"`. Breakpoint names come from `$breakpoints` in `s01_utilities`.
- Use without excess. A `g--` class is for a one-off spacing or size on a specific element in a template. When the same combination repeats, or it changes the look of a block, it is a block modifier instead.

## Layout blocks

An `l-` block only places things. It has two kinds of element: the block itself (the parent) and its children, called **areas**, with classes `__area` or `__area-<name>` (`.l-app__area-header`, `.l-app__area-main`).

- A layout's CSS only changes the position and, where needed, the size of its areas: display, grid or flex properties, gap, auto margins, order, scroll snap. It never sets colours, fonts, borders or other visual properties.
- The area class on a child is optional when it serves no purpose (every child of `l-stack` is an area and needs no class).
- An area may hold a `c-` block (the component may carry the area class itself) or another `l-` block.
- Layout modifiers (`.l-stack--tight`, `.l-board--snap`) follow the token rules above: declared design tokens such as `--stack-gap`, state where modifiers exist.

Zanshin's layouts: `l-app` (page shell: header and main areas), `l-board` (the four-column track, horizontal scroll with snap on narrow screens), `l-stack` (vertical rhythm between children).

## Enforcement

Stylelint (`stylelint-config-standard-scss`) with project rules, listed in [technology.md](technology.md#development-tools):

- BEM selectors with `c-` / `l-` / `g--` prefixes.
- Custom property naming: `--zn-sys-*` only in `_system-tokens.scss`, `--zn-sem-*` only in `_semantic-tokens.scss`, `--<block>-*` and `--<block>-privateState-*` in `s05_components` and `s06_layouts`.
- No Sass variables, literal colours or literal lengths in `s05_components` and `s06_layouts` (`declaration-strict-value` or equivalent).
- `!important` allowed only in `s04_global`.

Angular CLI settings: `src/styles.scss` is the only entry in the build `styles` array; `src/styles` is in `stylePreprocessorOptions.includePaths`; component schematics default to `style: none`. Web fonts are self-hosted in `public/`, no external CDN.

## Deviations from the reference

Where Zanshin differs from the reference course, and why:

- `s01_utilities` splits `_variables.scss` into `_system-variables.scss` (raw values) and `_semantic-variables.scss` (roles). *Why:* the semantic layer is a project decision and the two maps have different readers.
- Semantic colours are a separate token family `--zn-sem-*` generated from `$semantic-*` maps, instead of semantic names inside the system family (`--mlt-sys-color-principal-5`). *Why:* roles such as `surface`, `on-accent` and `focus-ring` are not tones of one palette colour, and a dark theme only needs to remap `--zn-sem-*`.
- Token prefix is `--zn-` instead of `--mlt-`. *Why:* `zn` is the project's selector prefix.
- Scale steps use named keys (`sm`, `md`, `lg`) or the design-token step numbers of the maps, not a fixed `0…10`. *Why:* the maps define the steps; the loops follow the maps.
- `s02_vendor` stays empty: no Angular Material, Bootstrap or any other UI library (see [technology.md](technology.md#frameworks-and-libraries)).
- The reference lets a component be either a CSS-only block or an Angular component with its own `.scss`. Zanshin always chooses the first: Angular components have no style files and only apply `c-`/`l-` classes (see [architecture.md](architecture.md#architectural-decisions)).
- The utilities namespace is `utilities` (the reference uses `utilidades`), and block, element, modifier and token names are in English.
- Layout CSS may set `gap` and scroll snap; the reference lists neither but allows anything that only positions or sizes areas.

## Where it is built

- Stage 1 of [implementation-plan.md](implementation-plan.md#stage-1-design-system-foundation) creates the layers, tokens, globals, the first `c-button` and `l-stack` blocks and a dev-only style sandbox, and verifies the Stylelint rules with a deliberately wrong file.
- Stage 3 adds the board blocks and the per-column modifiers.
- Stage 8 does the visual refinement and removes the sandbox from production builds.

Every text/background semantic colour pair must meet WCAG 2.2 AA contrast; see [accessibility.md](accessibility.md).

## Sources

- `llm_wiki/raw/design-system-reference-folder-structure.md`, ingested 2026-11-14. Summary: [design-system-folder-structure.md](design-system-folder-structure.md).
- `llm_wiki/raw/design-system-reference-bem.md`, ingested 2026-11-14. Summary: [design-system-bem.md](design-system-bem.md).
- `llm_wiki/raw/design-system-reference-tokens.md`, ingested 2026-11-14. Summary: [design-system-tokens.md](design-system-tokens.md).
- `llm_wiki/raw/design-system-reference-globals.md`, ingested 2026-11-14. Summary: [design-system-globals.md](design-system-globals.md).
- `llm_wiki/raw/design-system-reference-layout.md`, ingested 2026-11-14. Summary: [design-system-layout.md](design-system-layout.md).
