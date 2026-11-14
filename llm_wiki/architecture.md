---
description: The file organization, the naming conventions, the import patterns and the architectural decisions. Read when you add, move or rename a file, or change how parts of the code depend on each other.
---

# Architecture

What belongs here: how the code is organized and the conventions and decisions behind it. The styling rules below are gathered in [design-system.md](design-system.md), and the state, storage and import/export decisions in [data-format.md](data-format.md).

## File organization

The project follows the shared Angular project structure (`core/`, `features/`, `shared/`), adapted for a local-only app. The main card entity is `card`.

```
zanshin/
├── public/                       # static assets (favicon, icons, images)
├── src/
│   ├── main.ts
│   ├── index.html
│   ├── styles.scss               # global entry: @use of each sNN layer in order
│   ├── styles/
│   │   ├── s01_utilities/        # Sass only, emits no CSS; not loaded from styles.scss
│   │   │   ├── _index.scss
│   │   │   ├── _system-variables.scss    # raw values: $colors, $font-sizes, $paddings…
│   │   │   ├── _semantic-variables.scss  # roles → raw value keys: $semantic-colors…
│   │   │   ├── _functions.scss
│   │   │   └── _mixins.scss              # breakpoints, focus ring, visually-hidden, reduced motion
│   │   ├── s02_vendor/           # third-party CSS; empty, no UI library
│   │   │   └── _index.scss
│   │   ├── s03_base/
│   │   │   ├── _index.scss
│   │   │   ├── _system-tokens.scss       # --zn-sys-* on :root, from system variables
│   │   │   ├── _semantic-tokens.scss     # --zn-sem-* on :root, from system tokens
│   │   │   ├── _reset.scss
│   │   │   └── _typography.scss          # fonts
│   │   ├── s04_global/           # g--* modifier classes, one file per type
│   │   │   ├── _index.scss
│   │   │   ├── _g--color.scss
│   │   │   ├── _g--font-size.scss
│   │   │   ├── _g--margin.scss
│   │   │   └── _g--padding.scss
│   │   ├── s05_components/
│   │   │   ├── _index.scss
│   │   │   ├── _c-button.scss
│   │   │   ├── _c-header.scss
│   │   │   ├── _c-kanban-column.scss
│   │   │   ├── _c-kanban-card.scss
│   │   │   ├── _c-dialog.scss
│   │   │   ├── _c-field.scss             # label, input, textarea, error message
│   │   │   └── _c-notice.scss
│   │   └── s06_layouts/
│   │       ├── _index.scss
│   │       ├── _l-app.scss               # page shell: header + main
│   │       ├── _l-board.scss             # the four-column track, scroll/snap on mobile
│   │       └── _l-stack.scss             # vertical rhythm between children
│   └── app/
│       ├── app.ts | .html
│       ├── app.config.ts         # providers: zoneless, router, CDK
│       ├── app.routes.ts
│       ├── core/
│       │   ├── config/
│       │   │   ├── storage.config.ts    # storage key, current schemaVersion
│       │   │   └── board.config.ts      # the four column definitions
│       │   ├── layout/header/           # app title, Export / Import actions
│       │   ├── models/
│       │   │   ├── board.model.ts       # BoardState, BoardExport
│       │   │   ├── card.model.ts
│       │   │   └── column.model.ts      # ColumnId, ColumnDefinition
│       │   ├── services/
│       │   │   ├── board.service.ts          # signal state + all board mutations
│       │   │   ├── board-storage.service.ts  # local storage read/write
│       │   │   └── board-transfer.service.ts # JSON export / import
│       │   ├── utils/                   # id generation, file download, file reading
│       │   └── validators/
│       │       ├── board-import.validator.ts # shape + schema check of imported JSON
│       │       └── <validator>.ts            # form validators
│       ├── features/
│       │   ├── board/                   # board page (default route)
│       │   ├── card/
│       │   │   ├── card-new/
│       │   │   ├── card-edit/
│       │   │   ├── card-delete/
│       │   │   └── card.routes.ts
│       │   └── not-found/
│       └── shared/
│           ├── components/
│           │   ├── kanban-column/
│           │   ├── kanban-card/
│           │   ├── confirm-dialog/
│           │   └── notice/              # inline success / error messages
│           ├── directives/
│           └── pipes/
├── angular.json
├── package.json                  # includes the pinned `packageManager` (pnpm)
├── pnpm-lock.yaml
└── tsconfig.json
```

What lives where:

- **`core/`**: singletons and app-wide code with no UI except the layout. Models, config, services, validators, utilities.
- **`features/`**: routed pages. Each feature owns its route file and page components.
- **`shared/`**: reusable presentational components, directives and pipes. They receive data through inputs and report through outputs; they never inject `BoardService`.
- **`src/styles/`**: all styles in the app, as one global Sass project in six numbered layers that follow the reference structure in [source-design-system-reference-folder-structure.md](source-design-system-reference-folder-structure.md). Lower numbers come first in the cascade and may be used by higher ones, never the reverse. All six folders always exist:
  - `s01_utilities`: Sass variables, functions and mixins. Compile-time only; emits no CSS.
  - `s02_vendor`: third-party CSS. Empty in Zanshin.
  - `s03_base`: custom properties on `:root` (system and semantic tokens), reset, element typography and fonts.
  - `s04_global`: `g--*` global modifier classes, one file per type.
  - `s05_components`: `c-*` blocks with a visual identity (button, card, column, dialog…).
  - `s06_layouts`: `l-*` blocks that place things (grids, stacks, page shell) and have no visual identity of their own.
  - Each folder has an `_index.scss` that `@forward`s its partials; `styles.scss` `@use`s the indexes of `s02` to `s06` in order. The rules for each layer are in [design-system.md](design-system.md#layers).
- **Angular components have no style files.** Their templates apply the global `l-*` and `c-*` classes. An Angular component and its BEM block share a name: `zn-kanban-card` renders `c-kanban-card`, styled in `_c-kanban-card.scss`.

Deviations from the generic structure:

- Not used: `api/db.json`, `core/interceptors/`, `core/guards/`, `features/login/`, `features/access-denied/`, `*-api.service.ts`. There is no backend or authentication.
- `core/config/api.config.ts` is replaced by `storage.config.ts` and `board.config.ts`.
- `features/card/` has no `card.ts` list page, because the board page is the list.
- Angular components have no `.css`/`.scss` files; all styling lives in `src/styles/` (see above).
- `src/styles.css` is not used: the Angular CLI compiles `src/styles.scss` with Dart Sass directly.

## Naming conventions

**Files** (Angular style guide, no type suffix for components):

- Components: `kebab-case.ts | .html`, e.g. `kanban-card.ts`. Class name `KanbanCard` (no `Component` suffix).
- Services: `<name>.service.ts`, class `<Name>Service`.
- Models: `<entity>.model.ts`. Types and interfaces in `PascalCase`, no `I` prefix.
- Routes: `<feature>.routes.ts`, exported constant `<FEATURE>_ROUTES` (e.g. `CARD_ROUTES`).
- Config: `<name>.config.ts`, exported constants in `UPPER_SNAKE_CASE`.
- Tests: `<file>.spec.ts` next to the file under test.
- SCSS partials: leading underscore, `_kebab-case.scss`. Layout partials `_l-<name>.scss`, component partials `_c-<name>.scss`, one block per partial. Layer folders `sNN_<name>` with a two-digit number.

**Symbols:**

- Component selector prefix: `zn-` (e.g. `zn-kanban-card`). Directive attribute prefix: `zn` in camelCase (e.g. `znAutofocus`).
- Signals are named for their value, without a `$` suffix: `cards`, `isEmpty`. There are no Observables in the app, so the `$` suffix is not used.
- Booleans start with `is`, `has`, `can` or `should`.
- Outputs are named for the event without an `on` prefix: `cardMoved`, `confirmed`.
- Event handlers in templates use an `on` prefix: `onCardMoved()`.
- Column IDs are lowercase string literals: `'quiver' | 'nock' | 'draw' | 'loose'`.

**CSS (BEM):**

- Block prefixes: `c-` for components (`.c-button`, `.c-kanban-card`), `l-` for layouts (`.l-board`, `.l-stack`), `g--` for global modifiers (`.g--margin-top-md`). No other prefixes.
- Component block name = Angular component name without `zn-`, plus `c-`: `zn-kanban-card` → `.c-kanban-card`.
- Element: `.c-kanban-card__title`. Modifier: `.c-kanban-card--dragging`, `.c-kanban-column--loose`, `.l-stack--tight`. Layout areas: `.l-app__area-header`, `.l-app__area-main`.
- Each tag carries at most one block or element class, plus only modifiers of that block or element and `g--` classes.
- One block per partial, as a single `.c-<name> { }` with elements, modifiers and pseudo-classes nested with `&`. No nesting deeper than the element level; no styling by element type or ID outside `s03_base`.
- A `c-` block never sets its own outer position (margins, grid placement); that is the job of an `l-` block, a parent element or a `g--` class.
- System variables (raw values): plural Sass maps by category (`$colors`, `$font-sizes`, `$paddings`, `$radii`, `$shadows`, `$durations`), keys in kebab-case (`map.get($colors, 'vermilion-500')`). Single values use `$kebab-case`.
- Semantic variables: Sass maps prefixed `$semantic-` whose values are keys of a system map (`$semantic-colors: ('surface': 'paper-50', 'accent': 'vermilion-500')`).
- System tokens: `--zn-sys-<category>-<step>` for the raw scales and palette (`--zn-sys-color-vermilion-500`, `--zn-sys-font-size-3`, `--zn-sys-padding-md`).
- Semantic tokens: `--zn-sem-<category>-<role>` (`--zn-sem-color-surface`, `--zn-sem-color-on-accent`, `--zn-sem-color-column-draw`).
- Design tokens: `--<block>-<property>[-<variant>]`, using the block name without its `c-`/`l-` prefix (`--button-font-size`, `--kanban-card-title-color`, `--board-gap`). Declared at the top of the block; one value each; local to it.
- Private state: `--<block>-privateState-<property>` (`--button-privateState-background`). Declared after the design tokens; set only by the block's own modifiers and pseudo-classes. The `privateState` segment keeps its camelCase; custom property names are case-sensitive.

**Tests:** `describe` uses the class or function name; `it` reads as a sentence about behaviour (`it('replaces the board after confirmation')`).

## Import patterns

- **Path aliases** in `tsconfig.json`:
  - `@core/*` → `src/app/core/*`
  - `@features/*` → `src/app/features/*`
  - `@shared/*` → `src/app/shared/*`
- Use aliases across top-level folders; use relative paths only inside the same feature or component folder.
- **No barrel files** (`index.ts`). Import from the file that defines the symbol, to avoid circular imports and keep tree-shaking simple.
- **Dependency injection** with `inject()`, not constructor parameters.
- **Module boundaries:**
  - `core` may not import from `features` or `shared`.
  - `shared` may import models and utilities from `core`, never services that hold state, and never `features`.
  - `features` may import from `core` and `shared`, but not from other features.
  - Feature routes are lazy-loaded with `loadComponent` / `loadChildren` in `app.routes.ts`.
- Import order (enforced by lint): Angular and other packages, then `@core`, `@shared`, `@features`, then relative imports.
- **SCSS:** `@use` / `@forward` only, with namespaces (`@use 'sass:map';`, `@use '../s01_utilities' as utilities;`). `src/styles.scss` is the only global stylesheet in `angular.json`; add `src/styles` to `stylePreprocessorOptions.includePaths`.
  - `styles.scss` contains only `@use` lines, in layer order: `s02_vendor`, `s03_base`, `s04_global`, `s05_components`, `s06_layouts`. `s01_utilities` is not loaded there because it emits no CSS.
  - Each `_index.scss` only `@forward`s the partials of its folder.
  - `s03_base` token partials and `s04_global` may use `s01_utilities` variables. `s05_components` and `s06_layouts` may use `s01_utilities` **mixins and functions only**, never variables; they read values at runtime through `var(--zn-sem-…)` / `var(--zn-sys-…)`.
  - A layer never uses a higher-numbered layer.

## Architectural decisions

- **One root `BoardService` holds board state in signals.** Components read `computed` slices and call service methods to change state; nothing else writes to the state. *Why:* a single board does not justify NgRx, and signals with zoneless change detection keep rendering simple and fast.
- **Persistence is a separate service.** `BoardStorageService` is the only code that touches `localStorage`. `BoardService` saves through an `effect` after each change and loads once at start-up. *Why:* keeps storage concerns (quota, unavailable storage, corrupt data) isolated and lets tests replace storage with an in-memory fake.
- **Normalized board state:**

  ```ts
  interface BoardState {
    schemaVersion: 1;
    cards: Record<string, Card>;          // by id
    columns: Record<ColumnId, string[]>;  // ordered card ids per column
  }
  ```

  *Why:* moving or reordering a card only changes id arrays, which maps directly to CDK drag-drop events.
- **Columns are fixed configuration** in `board.config.ts` (id, label, order, short description), not user data. *Why:* the scope is one fixed four-column board; the config can become state later if custom columns are added.
- **Card IDs** come from `crypto.randomUUID()`. Timestamps are ISO 8601 strings.
- **Export/import format** wraps the state in an envelope: `{ app: 'zanshin', schemaVersion, exportedAt, board }`. Import flow: read file → `JSON.parse` in a `try` → validate with `board-import.validator.ts` → if the board has cards, open `confirm-dialog` → replace state in one assignment. Any failure leaves the current board unchanged. *Why:* imported files are untrusted and replacing data is destructive.
- **Schema versioning.** Both stored and exported data carry `schemaVersion`. A migration function per version upgrades older data; newer or unknown versions are rejected. If stored data is corrupt, the app starts with an empty board and keeps the raw value under a backup key instead of deleting it.
- **Routed card actions.** New, edit and delete are child routes (`/cards/new`, `/cards/:id/edit`, `/cards/:id/delete`) rendered over the board, with route params bound to component inputs. *Why:* follows the project structure's `<entity>-new/edit/delete` pattern and makes each action linkable and testable.
- **Drag and drop with CDK**, plus keyboard controls on each card (move to previous/next column, move up/down) and live-region announcements. *Why:* CDK drag-drop is not fully usable by keyboard on its own.
- **Global Sass architecture instead of Angular component styles.** All styles are one Dart Sass project in `src/styles/`, layered `s01_utilities` → `s02_vendor` → `s03_base` → `s04_global` → `s05_components` → `s06_layouts`. Angular components only apply classes. *Why:* one predictable cascade, BEM blocks that can be reused by any template, and a style layer that can be read and reviewed on its own.
- **Styling: values flow through five steps, compiled with Dart Sass.**
  1. **System variables** (`s01_utilities/_system-variables.scss`): raw values as Sass maps (`$colors`, `$font-sizes`, `$paddings`, `$radii`, `$shadows`, `$durations`…). Compile time only.
  2. **Semantic variables** (`s01_utilities/_semantic-variables.scss`): Sass maps that give roles to raw values by key (`$semantic-colors: ('surface': 'paper-50', 'accent': 'vermilion-500', …)`). Compile time only.
  3. **System tokens** (`s03_base/_system-tokens.scss`): `--zn-sys-*` custom properties on `:root`, generated with `@each` from the system variables. The full palette and every scale step.
  4. **Semantic tokens** (`s03_base/_semantic-tokens.scss`): `--zn-sem-*` custom properties on `:root`, generated from the semantic variables and pointing at system tokens (`--zn-sem-color-surface: var(--zn-sys-color-paper-50)`).
  5. **Design tokens**: custom properties declared at the top of each `c-`/`l-` block, one value each, set from semantic or system tokens.
  6. **Private state**: `--<block>-privateState-*` custom properties declared after the tokens, holding what the block looks like right now. Modifiers and pseudo-classes change only these.
  7. **CSS properties** read only the block's private state and design tokens.

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

    &--ghost {
      --button-privateState-background: transparent;
      --button-privateState-text-color: var(--button-color);
    }
  }
  ```

  Rules:
  - Design tokens may only reference semantic tokens, system tokens, another design token of the same block, or a keyword such as `transparent`. Never Sass variables, never literal values.
  - Colours always go through semantic tokens (`--zn-sem-color-*`). Scale steps that carry no role (font sizes, paddings, radii, durations) may use system tokens directly.
  - Style declarations may only use the block's own private state and design tokens.
  - Modifiers and pseudo-classes change the look by setting private state, never by redeclaring properties and never by changing design tokens. Each modifier group owns its own state, so modifiers combine.
  - Parents never set another block's private state. They may set a block's design tokens only where the block documents the token as a public customisation point.

  *Why:* raw values change in one place; semantic tokens give one place to retheme the app (dark mode is a remap of `--zn-sem-*` inside a theme selector or media query); design tokens make each block's adjustable surface explicit; private state keeps modifiers small and independent. The full rules are in [design-system.md](design-system.md#value-flow).
- **Visual direction.** Calm and restrained, inspired by kyūdō: off-white paper surfaces, ink-dark text, one vermilion accent (the target's centre), generous spacing and subtle motion. Each column has a quiet accent tied to its stage of the shot.
- **All components use `ChangeDetectionStrategy.OnPush`** and signal inputs/outputs; templates use built-in control flow.
