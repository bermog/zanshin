---
description: The board data in one place: the BoardState shape, the export envelope, schemaVersion and migrations, import validation and confirmation, and how each storage failure is handled. Read when you touch models, BoardService, BoardStorageService, BoardTransferService, the import validator or anything that reads or writes the board.
---

# Data format

What belongs here: the shape of the board data, how it is stored, exported and imported, and what happens when any of that fails. This page gathers statements that are spread across [product.md](product.md), [technology.md](technology.md), [architecture.md](architecture.md) and [implementation-plan.md](implementation-plan.md); those pages stay the source of truth and this page links to them.

## Principles

- **Local storage is the only persistence.** No backend, no database, no network. Data is per browser and per device; export/import is the only way to move or back up a board. See [technology.md](technology.md#technical-constraints).
- **Never lose user data.** Any release that changes the stored format adds a schema migration and keeps export/import compatible with older files. See [roadmap.md](roadmap.md#principles).
- **Stored and imported data is untrusted.** It is parsed safely and validated before use.
- **Import replaces, never merges.** The swap is all-or-nothing.

## Board state

Normalized, so that a move or reorder only changes id arrays (which maps directly to CDK drag-drop events):

```ts
interface BoardState {
  schemaVersion: 1;
  cards: Record<string, Card>;          // by id
  columns: Record<ColumnId, string[]>;  // ordered card ids per column
}
```

- `ColumnId` is `'quiver' | 'nock' | 'draw' | 'loose'`. Columns are fixed configuration in `core/config/board.config.ts` (id, label, order, description), not user data.
- A `Card` has a required title and an optional description, both with maximum lengths enforced by the form validators.
- Card ids come from `crypto.randomUUID()`. Timestamps are ISO 8601 strings.
- Models live in `core/models/` (`board.model.ts`, `card.model.ts`, `column.model.ts`). The storage key, backup key and current `schemaVersion` live in `core/config/storage.config.ts`.

## Services

Each concern has one service in `core/services/`; see [architecture.md](architecture.md#architectural-decisions).

- `BoardService` holds the state in signals and is the only writer: `addCard`, `updateCard`, `deleteCard`, `moveCard`, `replaceBoard`, plus computed selectors (`cardsByColumn`, `cardById`, `isEmpty`, `cardCount`). It loads once at start-up and saves through an `effect` after every change.
- `BoardStorageService` is the only code that touches `localStorage`: `load()`, `save(state)`, `isAvailable`. Tests replace it with an in-memory fake.
- `BoardTransferService` does JSON export and import.

## Export

`BoardTransferService.export()` wraps the state in an envelope and downloads it as `zanshin-board-YYYY-MM-DD.json`:

```ts
{ app: 'zanshin', schemaVersion, exportedAt, board }
```

A success notice is shown after export.

## Import

Flow, from Stage 6 of [implementation-plan.md](implementation-plan.md#stage-6-export-and-import):

1. The header's Import button opens a hidden file input limited to `.json`.
2. Read the file and `JSON.parse` inside a `try`.
3. Validate with `core/validators/board-import.validator.ts`: the envelope, the `app` value, the `schemaVersion`, the shape of every card and column, that every column id is valid, and that every id in `columns` exists in `cards` exactly once. The result is typed and carries readable error messages.
4. Apply migrations if the file is an older version (see below).
5. If the current board is empty, replace immediately. If it has cards, open `confirm-dialog` and replace only on confirm. Replacement is one assignment through `replaceBoard`.
6. Any error at any step shows a notice and leaves the current board unchanged.

Rejected with a clear message: malformed JSON, wrong `app`, unknown or newer `schemaVersion`, broken references. The validator has a test for every rejection case and for valid files. Export followed by import gives an identical board.

## Schema versioning and migrations

- Stored and exported data both carry `schemaVersion`. The current version is 1.
- A migration function per version upgrades older data; the pipeline applies to both stored and imported data. Newer or unknown versions are rejected.
- The first format change is planned for v1.2 (labels, due dates, checklists) as version 2; see [roadmap.md](roadmap.md#v12-richer-cards).

## Storage failures

From Stage 7 of [implementation-plan.md](implementation-plan.md#stage-7-data-resilience). No case throws an unhandled error or silently deletes data; each has a test using a fake storage that throws or returns bad data.

| Case | Behaviour |
|------|-----------|
| Storage unavailable (private browsing, disabled storage) | Run in memory and show a persistent warning that changes will not be saved. |
| `QuotaExceededError` on save (limit is roughly 5 MB per origin) | Keep the in-memory state, show an error notice and suggest exporting. |
| Corrupt stored data | Start with an empty board, copy the raw value to the backup key, and tell the user. |

Warnings and errors are shown through `shared/components/notice`, which uses a live region (see [accessibility.md](accessibility.md#screen-readers)).
