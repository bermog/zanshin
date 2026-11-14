---
description: The accessibility requirements in one place: WCAG 2.2 AA, keyboard alternatives for every action including card moves, focus management, live-region announcements, contrast and reduced motion. Read when you build or review any interaction, dialog, form or animation.
---

# Accessibility

What belongs here: what the app must do so that every feature works for keyboard and screen reader users. This page gathers requirements that are spread across [product.md](product.md), [technology.md](technology.md), [architecture.md](architecture.md) and [implementation-plan.md](implementation-plan.md); those pages stay the source of truth and this page links to them.

## Target

- **WCAG 2.2 AA** (see [technology.md](technology.md#technical-constraints)).
- Every feature works with the keyboard alone; no WCAG 2.2 AA violations reported by automated checks (see [product.md](product.md#business-objectives)).
- Lighthouse Accessibility score of 95 or higher on the deployed site.

## Keyboard

- Every action reachable by mouse has a keyboard path: add, edit and delete a card, open and close dialogs, export and import.
- **Moving cards:** CDK drag-drop is not fully usable by keyboard on its own, so each focused card has keyboard controls: move to the previous or next column, move up or down within the column. Visible hints (for example in the card's action menu or a tooltip) tell the user they exist. After a keyboard move, focus stays on the moved card. See [architecture.md](architecture.md#architectural-decisions) and Stage 5 of [implementation-plan.md](implementation-plan.md#stage-5-moving-cards).
- **Dialogs** (new, edit and delete card; import confirmation) trap focus inside while open, close on Escape, and return focus to the element that opened them. Built on `@angular/cdk/dialog` and `@angular/cdk/a11y`.
- Focus is always visible. The focus ring is a mixin in `s01_utilities/_mixins.scss` and a semantic colour role (`focus-ring`); see [design-system.md](design-system.md).

## Screen readers

- **Live region announcements** for every card move: "Moved 'Fix login' to Draw, position 2 of 5".
- `shared/components/notice` shows inline success and error messages in a live region, so import and export results and storage warnings are announced.
- Form errors are announced; invalid forms cannot be submitted (Signal Forms, Stage 4).
- Heading and landmark structure is part of the Stage 8 audit; the `l-app` layout is header + main.
- A `visually-hidden` mixin exists for text that only screen readers need.

## Visual

- Every text/background semantic colour pair meets AA contrast, checked when the tokens are created in Stage 1 (see [design-system.md](design-system.md#value-flow)).
- `prefers-reduced-motion` is respected everywhere, including drag animations. A reduced-motion wrapper mixin lives in `s01_utilities`.
- The layout is usable from 360 px wide upwards; on narrow screens the columns scroll horizontally with scroll snap, one column per snap point.

## Audit

Stage 8 of [implementation-plan.md](implementation-plan.md#stage-8-polish-and-accessibility) runs the full audit before release:

- Keyboard-only walkthrough of every feature.
- Screen reader check with VoiceOver or NVDA.
- Automated axe checks.
- Heading and landmark structure review.

Done when there are no automated violations and every feature works by keyboard and screen reader.
