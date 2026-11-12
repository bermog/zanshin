# Roadmap

What belongs here: the product's direction over time, organized as releases, from the first working build to ideas beyond v1.

Read with `product.md` (scope), `implementation-plan.md` (the stage-by-stage build for v1) and `architecture.md` (decisions that later releases must respect). This file says *what ships when*; the implementation plan says *how v1 is built*.

## Principles

- **Ship small, usable releases.** Every release is deployable and leaves the board working.
- **v1 scope is fixed.** New ideas go to "Later" unless they replace something already planned.
- **Never lose user data.** Any release that changes the stored format adds a schema migration and keeps export/import compatible with older files.
- **Local-first stays the default.** Even if sync is added later, the app must keep working with no account and no network.

## Overview

| Release | Theme                  | Implementation plan stages | Status  |
|---------|------------------------|----------------------------|---------|
| v0.1    | Foundation             | 0, 1, 2                    | Planned |
| v0.2    | Usable board           | 3, 4                       | Planned |
| v0.3    | Fluid board            | 5                          | Planned |
| v0.4    | Portable and safe data | 6, 7                       | Planned |
| v1.0    | Public launch          | 8, 9                       | Planned |
| v1.1    | Comfort                | —                          | Idea    |
| v1.2    | Richer cards           | —                          | Idea    |
| v2.0    | Many boards            | —                          | Idea    |
| Later   | Beyond the browser     | —                          | Idea    |

Status values: **Idea** (not committed), **Planned** (committed, not started), **In progress**, **Done**.

---

## v0.1: Foundation

Not visible to users. Everything later releases depend on.

- Project setup with pnpm, linting, Vitest and CI.
- Layered Sass project: system and semantic variables and tokens, base styles, and the first `l-`/`c-` blocks.
- Board state and local storage, fully tested, with no UI.

**Milestone:** CI is green and the core board logic passes all tests.

## v0.2: Usable board

The first version someone could actually use.

- Header, board page and the four columns: Quiver, Nock, Draw, Loose.
- Create, edit and delete cards, with validation and delete confirmation.
- Board saved to local storage and restored on reload.
- Not-found page.

**Milestone:** a user can manage cards on the board and nothing is lost on reload.

## v0.3: Fluid board

The kanban experience.

- Drag and drop between and within columns.
- Keyboard moves with screen reader announcements.

**Milestone:** every move works by mouse, touch and keyboard.

## v0.4: Portable and safe data

Users can trust the app with their data.

- Export the board to JSON; import with validation and overwrite confirmation.
- Schema versioning and migrations.
- Clear handling of unavailable storage, full storage and corrupt data.

**Milestone:** export → import gives an identical board, and no failure case loses data.

## v1.0: Public launch

The portfolio release.

- Responsive layout, visual polish and full accessibility pass.
- Public deployment with continuous delivery from `main`.
- Complete README with demo link and screenshots.
- Lighthouse Performance, Accessibility and Best Practices at 95 or higher.

**Milestone:** the live demo is linked from the portfolio.

---

## After v1

These are ideas, not commitments. Each one needs its scope agreed and `product.md`, `architecture.md` and `implementation-plan.md` updated before work starts.

### v1.1: Comfort

Small improvements that make daily use nicer, with no data format changes.

- Dark theme (remap of semantic tokens) with a toggle that follows the OS by default.
- Search or filter cards by text.
- Undo for the last delete or move.
- Keyboard shortcut for "new card" and a shortcut help panel.
- Sync between open tabs through the `storage` event.

### v1.2: Richer cards

Needs a schema migration (version 2).

- Labels with a small fixed colour set from the semantic tokens.
- Optional due date, with a subtle overdue state.
- Checklist inside a card.
- Card count limits per column (WIP limits), shown as a warning, never enforced.

### v2.0: Many boards

A structural change to the data model and routing.

- Multiple boards with a board switcher.
- Renamable columns, and possibly custom columns while keeping the archery set as the default template.
- Export and import of a single board or all boards.
- Archive for cards in Loose.

### Later: Beyond the browser

Only if the project grows past a portfolio piece.

- Installable PWA with offline support.
- IndexedDB storage if local storage limits become a problem.
- Optional account and cloud sync, keeping local-only mode as the default.
- End-to-end tests (Playwright) for the main flows.

---

## Keeping this file current

- Update the **Status** column when a release starts or ships.
- When a release ships, add its date and the version tag next to its heading.
- Move ideas between releases freely before work starts; once a release is "In progress", only remove scope, don't add it.
