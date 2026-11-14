---
description: The purpose, the target users, the key features and the business objectives of the product. Read when a task depends on what the product is for or who uses it.
---

# Product

What belongs here: what the product is, who it is for, and why it exists.

## Purpose

Zanshin is a single-board kanban app for tracking personal tasks from idea to done. It runs entirely in the browser: no account, no server, no setup. The board is saved to the browser's local storage and can be exported to and imported from a JSON file.

The app is a learning project and a portfolio piece. It stays small in scope, but the code and the interface should be finished to a professional standard.

The name and visual identity come from archery. *Zanshin* (残心, "remaining mind") is the term in Japanese archery (kyūdō) for the focused awareness held after the arrow is released. The four columns follow a shot from start to finish:

| Order | Column | Archery meaning                | Kanban meaning                 |
|-------|--------|--------------------------------|--------------------------------|
| 1     | Quiver | Arrows waiting to be chosen    | Backlog: ideas and future work |
| 2     | Nock   | Arrow fitted to the string     | Ready: chosen and prepared     |
| 3     | Draw   | Bow drawn, aim being taken     | In progress                    |
| 4     | Loose  | Arrow released                 | Done                           |

## Target users

- **Individual users** who want a quick, private, distraction-free board for personal tasks or small solo projects. They need fast card entry, easy drag-and-drop between columns, and confidence that their data stays on their own machine.
- **Portfolio reviewers** (recruiters, hiring managers, other developers) who will open the live demo and possibly the source code. They need an app that works immediately with no sign-up, looks polished, and shows clean, modern Angular code.

## Key features

- One board with four fixed columns: Quiver, Nock, Draw, Loose.
- Create, edit and delete cards. A card has a title (required) and an optional description.
- Delete asks for confirmation.
- Move cards between columns and reorder them within a column by drag and drop, with a keyboard alternative.
- Board state saved automatically to local storage and restored on reload.
- Export the board to a `.json` file.
- Import a board from a `.json` file. If the current board has any cards, the user must confirm before the import replaces it. Importing into an empty board needs no confirmation. An invalid file is rejected with a clear message and the current board is left untouched.
- Archery-themed, calm and polished interface that is responsive (desktop and mobile) and accessible.
- Not-found page for unknown routes.

### Out of scope for now

- Multiple boards, user-defined columns, renaming columns.
- Accounts, login, a backend, a database or cloud sync.
- Collaboration or sharing between users.
- Labels, due dates, assignees, attachments, comments.

## Business objectives

- **Showcase modern Angular.** Use current Angular 22 practice (standalone components, signals, zoneless change detection, Signal Forms, the new control flow) so the repository works as a code sample.
- **Show professional quality in a small scope.** Clean architecture, consistent naming, tested core logic, and a design system built from tokens.
- **Be easy to try.** Deployed publicly as a static site; usable in under 10 seconds with no sign-up.
- **Learning.** Practice SCSS architecture with Dart Sass ([design-system.md](design-system.md)), accessible drag and drop ([accessibility.md](accessibility.md)), and safe handling of persisted and imported data ([data-format.md](data-format.md)).

Success measures:

- Lighthouse scores of 95 or higher for Performance, Accessibility and Best Practices.
- Every feature works with the keyboard alone; no WCAG 2.2 AA violations reported by automated checks.
- Unit tests cover the services, validators and utilities in `core/`; all tests pass and lint is clean in CI.
- A README with a live demo link, screenshots, feature list and setup instructions.
