# Zanshin

A single-board kanban app that runs entirely in your browser. Archery-themed, calm and polished.

> **Status:** early stage. The project is planned and documented, but the Angular app has not been scaffolded yet. Nothing has shipped yet: the next release is v0.1 (Foundation). See the [Roadmap](#roadmap) below.

## About

Zanshin tracks personal tasks from idea to done. There is no account, no server and no setup. The board is saved to the browser's local storage and can be exported to and imported from a JSON file.

It is a learning project and a portfolio piece: small in scope, but built to a professional standard.

*Zanshin* (残心, "remaining mind") is the Japanese archery term for the focused awareness held after the arrow is released.

## Features

One board with four fixed columns that follow a shot from start to finish:

| Column | Meaning                        |
|--------|--------------------------------|
| Quiver | Backlog: ideas and future work |
| Nock   | Ready: chosen and prepared     |
| Draw   | In progress                    |
| Loose  | Done                           |

- Create, edit and delete cards (title required, description optional). Delete asks for confirmation.
- Move and reorder cards by drag and drop, with a keyboard alternative.
- Automatic save to local storage, restored on reload.
- Export the board to a `.json` file, or import one. Import replaces the current board and asks for confirmation if it has cards.
- Responsive (desktop and mobile) and accessible (WCAG 2.2 AA target).

## Planned stack

- Angular 22: standalone components, signals, zoneless change detection, Signal Forms, built-in control flow
- Angular CDK (drag-drop, dialog, a11y)
- TypeScript 6 (strict) and SCSS (Dart Sass) with a token-based design system and BEM blocks
- Vitest, angular-eslint, Prettier, Stylelint
- Node.js 22.12+ and pnpm

## Development

These commands are planned and will work once the app is scaffolded (v0.1).

| Task         | Command                                  |
|--------------|------------------------------------------|
| Install      | `pnpm install`                           |
| Dev server   | `pnpm start` (http://localhost:4200)     |
| Build        | `pnpm build` (output in `dist/`)         |
| Test         | `pnpm test` (`--coverage` for coverage)  |
| Lint         | `pnpm lint`                              |
| Format       | `pnpm format`                            |

## Roadmap

v1.0 is a small, fixed scope; everything after it is an idea, not a commitment. Full detail in [`llm_wiki/roadmap.md`](llm_wiki/roadmap.md).

| Release | Theme                  | Status  |
|---------|------------------------|---------|
| v0.1    | Foundation: setup, layered Sass design system, board state and storage | Planned |
| v0.2    | Usable board: columns, card create/edit/delete | Planned |
| v0.3    | Fluid board: drag and drop, keyboard moves | Planned |
| v0.4    | Portable and safe data: export/import, schema versioning | Planned |
| v1.0    | Public launch: responsive, accessible, deployed | Planned |
| v1.1    | Comfort: dark theme, search, undo, shortcuts | Idea |
| v1.2    | Richer cards: labels, due dates, checklists | Idea |
| v2.0    | Many boards, renamable columns, archive | Idea |
| Later   | PWA, IndexedDB, optional sync, Playwright e2e | Idea |

## Documentation

[`llm_wiki/`](llm_wiki/) is the source of truth for the project:

- [product](llm_wiki/product.md): purpose, users, features, scope
- [technology](llm_wiki/technology.md): stack, tooling, constraints
- [architecture](llm_wiki/architecture.md): structure, conventions, decisions
- [roadmap](llm_wiki/roadmap.md): releases from v0.1 to ideas beyond v1

[`AGENTS.md`](AGENTS.md) points coding agents to these pages.

## License

MIT. See [`LICENSE`](LICENSE).
