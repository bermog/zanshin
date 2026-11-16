---
description: The frameworks, the libraries, the commands for build, test, lint and run, deployment, and the technical constraints. Read when you add a dependency, run a command or choose a tool.
---

# Technology

What belongs here: the tech stack, tooling, and technical limits of the project.

## Frameworks and libraries

- **Angular 22** (standalone components only, no NgModules).
  - **Signals** for all component and application state (`signal`, `computed`, `effect`, `linkedSignal`, `input`, `output`, `model`).
  - **Zoneless change detection** (no `zone.js`).
  - **Signal Forms** for the card create/edit forms.
  - **Built-in control flow** (`@if`, `@for`, `@switch`, `@defer`); no `*ngIf` / `*ngFor`.
  - **Angular Router** with lazy-loaded feature routes and component input binding.
- **Angular CDK**: `@angular/cdk/drag-drop` for moving cards between and within columns; `@angular/cdk/dialog` and `@angular/cdk/a11y` for the confirmation dialog and focus management.
- **TypeScript 6** in strict mode (`strict`, `strictTemplates`, `noUncheckedIndexedAccess`).
- **SCSS compiled with Dart Sass** (the `sass` package used by the Angular CLI). Module system only: `@use` / `@forward` and built-in modules such as `sass:map` and `sass:math`; no `@import`, no global built-in functions (`map-get`, etc.), no LibSass or Node Sass.
- **No RxJS in app code.** Use signals, `resource` / `httpResource` and Promises instead. The `rxjs` package stays installed only because Angular's own packages declare it as a peer dependency; never import from `rxjs` in `src/` (enforced by an ESLint `no-restricted-imports` rule). Local storage and file reads are synchronous or Promise-based, so no Observables are needed.
- No UI component library (no Angular Material components, no Bootstrap, no Tailwind). All visuals are built from the project's own tokens and its `c-`/`l-` BEM blocks in `src/styles/`.
- No state-management library (NgRx etc.). A root signal-based service is enough for one board.

## Development tools

- **Node.js** 22.12 or later (LTS) and **pnpm** (the only package manager; do not use npm or yarn). The pnpm version is pinned through the `packageManager` field in `package.json` (activate with `corepack enable`). The lockfile is `pnpm-lock.yaml` and is committed.
- **Angular CLI 22**.

| Task                   | Command                                   |
|------------------------|-------------------------------------------|
| Install dependencies   | `pnpm install`                            |
| Run dev server         | `pnpm start` (`ng serve`), http://localhost:4200 |
| Production build       | `pnpm build` (`ng build`) → `dist/`    |
| Unit tests (Vitest)    | `pnpm test` (`ng test`)                   |
| Tests with coverage    | `pnpm test --coverage`                    |
| Lint                   | `pnpm lint` (`ng lint`, angular-eslint) |
| Format                 | `pnpm format` (Prettier)                  |
| Generate a component   | `pnpm ng g c features/<name>` (no style file, standalone, no `.component` suffix) |

- **Vitest** is the unit test runner (Angular CLI default). Test files sit next to their source as `<file>.spec.ts`. How tests are written and what each stage must test is in [testing.md](testing.md).
- **angular-eslint** for TypeScript and template linting; **Prettier** for formatting (`.prettierrc` at the root).
- **Stylelint** (`stylelint-config-standard-scss`) with rules for: BEM selectors with `c-` / `l-` / `g--` prefixes; custom property naming (`--zn-sys-*` only in `s03_base/_system-tokens.scss`, `--zn-sem-*` only in `s03_base/_semantic-tokens.scss`, `--<block>-*` and `--<block>-privateState-*` in `s05_components` and `s06_layouts`); no Sass variables, literal colours or literal lengths in `s05_components` and `s06_layouts` (`declaration-strict-value` or equivalent); `!important` only in `s04_global`.
- **Angular CLI style settings:** `src/styles.scss` is the only entry in the build `styles` array; component schematics default to `style: none` in `angular.json`, so generated components get no style file.
- CI (GitHub Actions) installs with `pnpm install --frozen-lockfile`, then runs lint, tests and a production build on every push and pull request.
- Deployed as a static site on **GitHub Pages**, from CI on merge to `main`. Deep links need a `404.html` copy of `index.html` as the SPA fallback. *Why:* the repository is already on GitHub, so no extra account or service is needed.
- **Playwright** is the end-to-end test runner. The suites (create, move, export → import) are not part of v1; they are added when the project grows, as described in [roadmap.md](roadmap.md#later-beyond-the-browser).

## Technical constraints

- **No backend.** No HTTP calls, no database, no authentication. `api/db.json`, HTTP interceptors, login, guards and access-denied from the generic structure are not used.
- **Local storage is the only persistence.**
  - Limit is roughly 5 MB per origin. Writes must catch `QuotaExceededError` and tell the user.
  - Storage may be unavailable (private browsing, disabled storage). The app must still run in memory and warn that changes will not be saved.
  - Data is per browser and per device. Export/import is the only way to move or back up a board.
  - Stored and imported data is untrusted: it must be parsed safely and validated before use.
- **Versioned data format.** Stored and exported JSON carries a `schemaVersion`. Unknown or newer versions are rejected with a clear message; older versions are migrated in code.
- **Import replaces, never merges.** Importing into a non-empty board requires explicit user confirmation, and the swap is all-or-nothing.
- **Browsers:** current evergreen versions of Chrome, Edge, Firefox and Safari (desktop and mobile). No IE or legacy support.
- **Accessibility:** WCAG 2.2 AA. Full keyboard support, including moving cards without a mouse; visible focus; sufficient colour contrast; respects `prefers-reduced-motion`. The full list of requirements is in [accessibility.md](accessibility.md).
- **Responsive:** usable from 360 px wide upwards. On narrow screens the columns scroll horizontally with scroll snap, one column per snap point; they never stack.
- **Performance:** initial bundle under the Angular CLI default budgets; lazy-load feature routes.
- **No third-party tracking or analytics.** User data never leaves the browser.

## Sources

- `llm_wiki/raw/design-system-reference-folder-structure.md`, ingested 2026-11-14. Summary: [design-system-folder-structure.md](design-system-folder-structure.md).
