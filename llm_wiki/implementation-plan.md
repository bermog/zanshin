---
description: The stages in which v1 is built, what each stage delivers and how to tell a stage is done. Read when you start a stage, check what is already built or decide what to build next.
---

# Implementation Plan

What belongs here: the order in which Zanshin is built, what each stage delivers, and how to tell a stage is done.

Read with [product.md](product.md), [technology.md](technology.md) and [architecture.md](architecture.md). Those files are the source of truth for scope, stack and conventions. This plan only sequences the work. The releases that group the stages are in [roadmap.md](roadmap.md).

## How to use this plan

- Work through the stages in order. Each stage builds on the previous one and ends with an app that builds, passes lint and passes tests.
- A stage is complete only when every item under **Done when** is true.
- Keep each stage on its own branch and merge it with a pull request. Small commits inside a stage are fine.
- If a stage reveals that a decision in [architecture.md](architecture.md) or [technology.md](technology.md) is wrong, update that file in the same pull request.

## Overview

| Stage | Name                         | Result                                                     |
|-------|------------------------------|------------------------------------------------------------|
| 0     | Project setup                | Empty Angular 22 app with tooling, CI and folder skeleton  |
| 1     | Design system foundation     | Tokens, base styles and a style sandbox                    |
| 2     | Domain core                  | Models, config, board state and storage, fully tested      |
| 3     | Board layout                 | Header, board page and four columns showing cards          |
| 4     | Card management              | Create, edit and delete cards through routed forms         |
| 5     | Moving cards                 | Drag and drop plus keyboard moves                          |
| 6     | Export and import            | JSON export, validated import, overwrite confirmation      |
| 7     | Data resilience              | Schema migration, storage failures, corrupt data handling  |
| 8     | Polish and accessibility     | Responsive layout, motion, a11y audit, visual refinement   |
| 9     | Release                      | Deployment, README, Lighthouse targets met                 |

---

## Stage 0: Project setup

**Goal:** a clean, empty project with every tool working before any feature code is written.

Tasks:

- Create the app with Angular CLI 22: standalone, SCSS global stylesheet, routing, zoneless, selector prefix `zn`, strict mode. Set the component schematic default to `style: none`.
- Set pnpm as the package manager: `packageManager` field in `package.json`, `pnpm-lock.yaml` committed, `.npmrc` if needed.
- Remove `zone.js` if the CLI added it. Confirm `provideZonelessChangeDetection()` (or the v22 default) in `app.config.ts`.
- Add path aliases `@core/*`, `@features/*`, `@shared/*` in `tsconfig.json`. Enable `noUncheckedIndexedAccess`.
- Add `src/styles` to `stylePreprocessorOptions.includePaths` in `angular.json`. Create the six layer folders `s01_utilities`, `s02_vendor`, `s03_base`, `s04_global`, `s05_components`, `s06_layouts`, each with an empty `_index.scss`, and `@use` `s02` to `s06` in order from `styles.scss` (see [design-system.md](design-system.md#layers)).
- Install and configure angular-eslint, Prettier and Stylelint (BEM selectors with `c-`/`l-` prefixes).
- Add ESLint rules: `no-restricted-imports` for `rxjs`, import order, module boundaries between `core`, `shared` and `features`.
- Confirm Vitest runs with `pnpm test`.
- Add `package.json` scripts: `start`, `build`, `test`, `lint`, `format`.
- Create the empty folder skeleton from [architecture.md](architecture.md#file-organization) (use `.gitkeep` where a folder is still empty).
- Add a GitHub Actions workflow: `pnpm install --frozen-lockfile`, lint, test, build.
- Add `.editorconfig` and `.gitignore`. `README.md` and `LICENSE` (MIT) already exist; leave them as they are.

Done when:

- `pnpm start`, `pnpm build`, `pnpm test`, `pnpm lint` and `pnpm format` all succeed.
- CI passes on the first pull request.
- An import from `rxjs` in `src/` fails lint.

## Stage 1: Design system foundation

**Goal:** the visual base that every component will use, before any component exists.

Tasks:

- `s01_utilities/_system-variables.scss`: raw values as Sass maps: `$colors` (paper, ink, vermilion accent, a quiet accent per column), `$font-families`, `$font-sizes`, `$font-weights`, `$line-heights`, `$paddings`, `$radii`, `$shadows`, `$durations`, `$easings`, `$breakpoints`.
- `s01_utilities/_semantic-variables.scss`: role maps pointing at system keys: `$semantic-colors` (surface, surface-raised, on-surface, muted, accent, on-accent, border, focus-ring, danger, one per column), plus any other roles needed (e.g. `$semantic-elevations`).
- `s01_utilities/_mixins.scss`: breakpoints, focus ring, visually-hidden, reduced-motion wrapper. `s01_utilities/_functions.scss`: helpers for reading the maps (may start empty).
- `s04_global`: `_g--color.scss`, `_g--font-size.scss`, `_g--margin.scss`, `_g--padding.scss`, each generated from the system maps with the responsive variants after the base classes. `s02_vendor/_index.scss` stays empty.
- `s03_base/_system-tokens.scss`: generate every `--zn-sys-*` on `:root` with `sass:map` and `@each` (`--zn-sys-color-<key>`, `--zn-sys-font-size-1…n`, `--zn-sys-padding-xs…xl`…).
- `s03_base/_semantic-tokens.scss`: generate every `--zn-sem-*` on `:root` from the semantic maps, each as a `var()` of a system token.
- `s03_base/_reset.scss` and `_typography.scss`. Choose and load the web fonts (self-hosted in `public/`, no external CDN).
- Fill each `_index.scss` with `@forward`s.
- Configure the Stylelint rules for the layers (see [technology.md](technology.md#development-tools)) and verify them with a deliberately wrong sample file.
- First component and layout: `s05_components/_c-button.scss` (with a `--ghost` modifier that changes private state) and `s06_layouts/_l-stack.scss` (with a `--tight` modifier), following the token, state and layout rules in [design-system.md](design-system.md#value-flow), to copy from later.
- Build a temporary style sandbox (a dev-only route or page) showing colours, type, spacing and focus states. Remove it, or exclude it from production, at the end of Stage 8.
- Check colour contrast for every text/background semantic colour pair.

Done when:

- All system and semantic tokens exist and are documented with a short comment; `s01_utilities` emits no CSS.
- `c-button` declares its design tokens, then its private state, at the top of its block; its properties read only those; its modifier only sets private state.
- Stylelint rejects a Sass variable, a literal colour, a missing `c-`/`l-` prefix or an `!important` in `s05_components` / `s06_layouts`.
- Every text/background pair meets WCAG 2.2 AA contrast.
- No component code exists yet; the sandbox renders correctly in light mode.

## Stage 2: Domain core

**Goal:** all board logic working and tested, with no UI.

Tasks:

- Models: `Card`, `ColumnId`, `ColumnDefinition`, `BoardState`, `BoardExport`.
- Config: `board.config.ts` (the four columns: id, label, order, description) and `storage.config.ts` (storage key, backup key, current `schemaVersion`).
- Utils: id generation (`crypto.randomUUID()`), current ISO timestamp, an empty board factory.
- `BoardStorageService`: `load()`, `save(state)`, `isAvailable`. Only this service touches `localStorage`. Basic error handling for now; full handling comes in Stage 7.
- `BoardService` with signal state and methods:
  - `addCard(columnId, input)`, `updateCard(id, changes)`, `deleteCard(id)`
  - `moveCard(id, toColumnId, toIndex)`
  - `replaceBoard(state)`
  - computed selectors: `cardsByColumn`, `cardById(id)`, `isEmpty`, `cardCount`
- Load state once at start-up; save through an `effect` on every change.
- Unit tests for every method and selector, using an in-memory fake for storage.

Done when:

- All `BoardService` and `BoardStorageService` behaviour is covered by passing tests.
- State survives a simulated reload in tests (save, new service instance, load).
- No component imports `localStorage` or touches state directly.

## Stage 3: Board layout

**Goal:** a read-only board that shows real data in the final layout.

Tasks:

- `app.routes.ts`: lazy-load the board as the default route and add the `not-found` wildcard route.
- `core/layout/header`: app name and placeholder Export / Import buttons (wired up in Stage 6).
- `features/board`: page that reads `cardsByColumn` and renders four columns in order.
- `shared/components/kanban-column` (`.c-kanban-column`): title, card count, empty state, card list. Inputs only.
- `shared/components/kanban-card` (`.c-kanban-card`): title and a short description preview. Inputs only.
- Layouts `l-app` (header + main) and `l-board` (the four-column track).
- A column modifier per stage (`.c-kanban-column--quiver`, etc.) that only reassigns the column's design tokens (e.g. `--kanban-column-accent`) to the matching semantic token (`--zn-sem-color-column-quiver`).
- `features/not-found`: simple page with a link back to the board.
- Temporary seed data for development (a dev-only helper, never shipped).

Done when:

- The board shows four columns with their cards from `BoardService`, in the right order.
- Empty columns show a clear empty state.
- Every `c-`/`l-` block lives in its own partial, declares its design tokens from semantic or system tokens only, and its declarations use only those tokens; Angular components have no style files; Stylelint passes.
- Component tests cover rendering of cards, counts and empty states.

## Stage 4: Card management

**Goal:** full create, edit and delete for cards.

Tasks:

- `card.routes.ts` with child routes: `cards/new` (with a target column query param), `cards/:id/edit`, `cards/:id/delete`. Bind route params to component inputs.
- Show these routes over the board in a dialog opened with `@angular/cdk/dialog` (see [technology.md](technology.md#frameworks-and-libraries)), with focus trapped inside and returned to the trigger on close.
- `card-new` and `card-edit`: Signal Forms with validation (title required and trimmed, maximum lengths for title and description). Show errors accessibly.
- `card-delete`: confirmation built on `shared/components/confirm-dialog`.
- "Add card" button in each column; edit and delete actions on each card.
- Unknown card id in an edit or delete route → redirect to the board with a notice, or to not-found.
- `shared/components/notice`: inline success and error messages with a live region.

Done when:

- A card can be created in any column, edited and deleted, and the changes survive a reload.
- Invalid forms cannot be submitted and errors are announced to screen readers.
- Escape closes any dialog and focus returns to where it came from.
- Tests cover form validation, the three routes and the confirm dialog.

## Stage 5: Moving cards

**Goal:** cards move between and within columns by mouse, touch and keyboard.

Tasks:

- Connect the four columns with CDK drag-drop (`cdkDropListGroup`). Call `moveCard` on drop.
- Drag preview and placeholder styled through design tokens; `.c-kanban-card--dragging` modifier reassigns tokens only. CDK's preview and placeholder elements get `c-kanban-card` modifier classes, since there are no component-scoped styles.
- Keyboard controls on each focused card: move to the previous or next column, move up or down within the column. Add visible hints (for example in the card's action menu or a tooltip).
- Announce every move through a live region ("Moved 'Fix login' to Draw, position 2 of 5").
- Keep focus on the moved card after a keyboard move.
- Respect `prefers-reduced-motion` for drag animations.

Done when:

- Cards can be reordered and moved between all columns with mouse, touch and keyboard alone.
- Every move is announced and persisted.
- Tests cover `moveCard` edge cases (same position, first, last, empty column) and the keyboard handlers.

## Stage 6: Export and import

**Goal:** users can back up and restore the board as a JSON file.

Tasks:

- `BoardTransferService.export()`: build the `{ app: 'zanshin', schemaVersion, exportedAt, board }` envelope and download it as `zanshin-board-YYYY-MM-DD.json`.
- `board-import.validator.ts`: check the envelope, `app` value, `schemaVersion`, the shape of every card and column, that every column id is valid, and that every id in `columns` exists in `cards` exactly once. Return a typed result with readable error messages.
- `BoardTransferService.import(file)`: read the file, `JSON.parse` inside `try`, validate, then:
  - board empty → replace immediately;
  - board not empty → open the confirm dialog, replace only on confirm;
  - any error → show a notice and leave the board unchanged.
- Wire the header's Export and Import buttons. Import uses a hidden file input limited to `.json`.
- Show a success notice after export and after import.

Done when:

- Exporting and then importing the file gives an identical board.
- Importing into a non-empty board always asks for confirmation; cancelling changes nothing.
- Malformed JSON, wrong `app`, unknown `schemaVersion` and broken references are each rejected with a clear message.
- The validator has tests for every rejection case and for valid files.

## Stage 7: Data resilience

**Goal:** the app never loses or corrupts data and never crashes because of storage.

Tasks:

- Migration pipeline: a function per schema version, applied to both stored and imported data. Add a test fixture for version 1.
- Storage unavailable (private mode, disabled storage): run in memory and show a persistent warning that changes won't be saved.
- `QuotaExceededError` on save: keep the in-memory state, show an error notice and suggest exporting.
- Corrupt stored data: start with an empty board, copy the raw value to the backup key, and tell the user.

Done when:

- Each failure case is covered by a test using a fake storage that throws or returns bad data.
- No failure case throws an unhandled error or silently deletes data.

## Stage 8: Polish and accessibility

**Goal:** the app looks and feels finished on every screen size.

Tasks:

- Responsive layout from 360 px: on narrow screens the four columns scroll horizontally with scroll snap, one column per snap point (see [technology.md](technology.md#technical-constraints)).
- Visual refinement against the kyūdō direction: spacing, typography, column accents, subtle transitions, focus styles.
- Favicon, app icons, page title and meta description.
- Accessibility audit: keyboard-only walkthrough of every feature, screen reader check (VoiceOver or NVDA), automated axe checks, heading and landmark structure.
- Check `prefers-reduced-motion` everywhere.
- Remove or exclude the style sandbox and dev seed data from production builds.

Done when:

- No automated a11y violations; every feature works by keyboard and screen reader.
- Layout works at 360 px, tablet and desktop widths in Chrome, Edge, Firefox and Safari.
- No Sass variables, literal colours, sizes or durations remain in `s05_components` or `s06_layouts`; every block's design tokens are listed at the top of the block.

## Stage 9: Release

**Goal:** a public, documented portfolio piece.

Tasks:

- Deploy to GitHub Pages from CI on merge to `main`, with a `404.html` copy of `index.html` as the SPA fallback for deep links (see [technology.md](technology.md#development-tools)).
- Run Lighthouse on the deployed site and fix anything under the targets.
- Update the README for the release: live demo link, screenshots or GIF, how to run locally with pnpm, testing, and the archery column meanings. Keep the existing sections.
- Tag `v1.0.0`.

Done when:

- The live site loads with no errors and deep links work.
- Lighthouse Performance, Accessibility and Best Practices are all 95 or higher.
- The README is complete and CI is green on `main`.

---

## After v1 (not planned yet)

This plan covers v1 only. Ideas for later releases, grouped by release, live in [roadmap.md](roadmap.md#after-v1); the "out of scope" list in [product.md](product.md#out-of-scope-for-now) is where they come from. Before work on any of them starts, this plan gets new stages for it.
