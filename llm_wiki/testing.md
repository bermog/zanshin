---
description: Testing in one place: Vitest and the test commands, spec file placement and naming, the in-memory storage fakes, the tests each implementation stage requires, CI, and the later Playwright suites. Read when you write, name or run a test, or check what a stage must test before it is done.
---

# Testing

What belongs here: how the project is tested, where tests live and what each stage must test. This page gathers statements that are spread across [product.md](product.md), [technology.md](technology.md), [architecture.md](architecture.md), [data-format.md](data-format.md) and [implementation-plan.md](implementation-plan.md); those pages stay the source of truth and this page links to them.

## Principles

- The core logic is tested. Unit tests cover the services, validators and utilities in `core/`; all tests pass and lint is clean in CI. See [product.md](product.md).
- Each implementation stage ends with an app that builds, passes lint and passes tests. See [implementation-plan.md](implementation-plan.md).

## Tools and commands

- **Vitest** is the unit test runner (Angular CLI default). See [technology.md](technology.md#development-tools).

| Task                | Command                 |
|---------------------|-------------------------|
| Unit tests          | `pnpm test` (`ng test`) |
| Tests with coverage | `pnpm test --coverage`  |

- CI (GitHub Actions) installs with `pnpm install --frozen-lockfile`, then runs lint, tests and a production build on every push and pull request.
- **Playwright** is the end-to-end test runner. The suites (create, move, export → import) are not part of v1; they are added when the project grows, as described in [roadmap.md](roadmap.md#later-beyond-the-browser).

## Placement and naming

- Test files sit next to the file under test as `<file>.spec.ts`. See [architecture.md](architecture.md#naming-conventions).
- `describe` uses the class or function name; `it` reads as a sentence about behaviour (`it('replaces the board after confirmation')`).

## Storage fakes

- `BoardStorageService` is the only code that touches `localStorage`, so tests replace it with an in-memory fake. See [architecture.md](architecture.md#architectural-decisions) and [data-format.md](data-format.md#services).
- Storage failure tests use a fake storage that throws or returns bad data. See [data-format.md](data-format.md#storage-failures).

## Tests required by stage

| Stage | Tests |
|-------|-------|
| [0: Project setup](implementation-plan.md#stage-0-project-setup) | Vitest runs with `pnpm test`; the CI workflow runs lint, test and build. |
| [2: Domain core](implementation-plan.md#stage-2-domain-core) | Unit tests for every `BoardService` method and selector, with an in-memory fake for storage. All `BoardService` and `BoardStorageService` behaviour is covered. State survives a simulated reload (save, new service instance, load). |
| [3: Board layout](implementation-plan.md#stage-3-board-layout) | Component tests cover rendering of cards, counts and empty states. |
| [4: Card management](implementation-plan.md#stage-4-card-management) | Form validation, the three routes and the confirm dialog. |
| [5: Moving cards](implementation-plan.md#stage-5-moving-cards) | `moveCard` edge cases (same position, first, last, empty column) and the keyboard handlers. |
| [6: Export and import](implementation-plan.md#stage-6-export-and-import) | The validator has a test for every rejection case and for valid files. Export followed by import gives an identical board. |
| [7: Data resilience](implementation-plan.md#stage-7-data-resilience) | A test fixture for schema version 1 for the migration pipeline. Each storage failure case has a test with a fake storage that throws or returns bad data. |
| [9: Release](implementation-plan.md#stage-9-release) | The README explains how to run the tests. |
