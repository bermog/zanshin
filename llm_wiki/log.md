# Log

The record of each operation on the wiki, oldest first. Append only. The entry format is in [AGENTS.md](AGENTS.md#log).

## [2026-11-14] create | wiki scaffolded

- created: raw/
- created: AGENTS.md
- created: index.md
- created: log.md
- created: agent-procedures.md
- created: instruction-review.md
- created: instruction-wording.md
- created: skill-packaging.md

## [2026-11-14] lint | 5 fixed, 0 reported

- fixed: product.md (added description)
- fixed: technology.md (added description)
- fixed: architecture.md (added description)
- fixed: implementation-plan.md (added description, added to index.md)
- fixed: roadmap.md (added description, added to index.md)

## [2026-11-14] lint | 2 fixed, 6 reported

- fixed: implementation-plan.md (turned the backtick references to product.md, technology.md and architecture.md into links; added links to roadmap.md)
- fixed: roadmap.md (turned the backtick references to product.md, implementation-plan.md and architecture.md into links)

## [2026-11-14] lint | 1 fixed, 0 reported

- fixed: implementation-plan.md (removed the optional storage-event sync from Stage 7 and the optional dark theme from Stage 8; both are v1.1 in roadmap.md, which the user confirmed as the source of truth)

## [2026-11-14] lint | 9 fixed, 0 reported

- fixed: implementation-plan.md (Stage 0 and Stage 9 no longer create README.md and LICENSE, which already exist; Stage 4 settles on @angular/cdk/dialog as technology.md already states; Stage 8 and Stage 9 point to the open decisions in technology.md; After v1 is a pointer to roadmap.md instead of a second list; overview table realigned)
- fixed: roadmap.md (v0.2 marks local storage as built in v0.1; v1.1 says no board schema changes instead of no data format changes; Playwright moved out of the Later release into technology.md)
- fixed: technology.md (new Open decisions section: static host, narrow-screen layout, end-to-end tests; description updated)
- fixed: index.md (technology.md description)

## [2026-11-14] lint | 4 fixed, 0 reported

- fixed: technology.md (Open decisions section replaced by decisions from the user: GitHub Pages as host, Playwright as e2e runner, horizontal scroll with snap on narrow screens; description updated)
- fixed: implementation-plan.md (Stage 8 and Stage 9 use those decisions; licence is not touched in any stage, it stays MIT)
- fixed: roadmap.md (Playwright e2e tests back in Later, pointing at technology.md)
- fixed: index.md (technology.md description)

## [2026-11-14] lint | 0 fixed, 3 reported

- fixed: none
- reported: 3 suggested pages (design-system, accessibility, data-format), see the lint report

## [2026-11-14] lint | 3 created from the lint suggestions

- created: design-system.md
- created: accessibility.md
- created: data-format.md
- updated: index.md
- updated: product.md, architecture.md, technology.md (links to the new pages)

## [2026-11-14] ingest | llm_wiki/raw/design-system-reference-folder-structure.md

- created: raw/design-system-reference-folder-structure.md (page 17 of the reference design-system course)
- created: source-design-system-reference-folder-structure.md
- updated: design-system.md (six layers, load order, utilities import, access rules, Deviations from the reference section)
- updated: architecture.md (tree, what lives where, SCSS import rules, decision)
- updated: technology.md (Stylelint folder names)
- updated: implementation-plan.md (Stage 0 folders, Stage 1 tasks and Done when, Stage 8 Done when)
- updated: index.md

## [2026-11-14] ingest | llm_wiki/raw/design-system-reference-bem.md, -tokens.md, -globals.md, -layout.md

- created: raw/design-system-reference-bem.md, raw/design-system-reference-tokens.md, raw/design-system-reference-globals.md, raw/design-system-reference-layout.md (pages 13 to 16 of the reference design-system course)
- created: source-design-system-reference-bem.md, source-design-system-reference-tokens.md, source-design-system-reference-globals.md, source-design-system-reference-layout.md
- updated: design-system.md (value flow with private state and the adopted modifier rule, BEM rules, new Global modifiers and Layout blocks sections, enforcement, deviations)
- updated: architecture.md (BEM bullets, private state naming, c-button example and token rules)
- updated: technology.md (Stylelint: g-- prefix, privateState naming, !important only in s04_global)
- updated: implementation-plan.md (Stage 1 first blocks and Done when)
- updated: index.md
- conflict adopted: modifiers change private state, never design tokens (recorded in source-design-system-reference-tokens.md)

## [2026-11-16] lint | 5 fixed, 0 reported

- fixed: renamed source-design-system-reference-folder-structure.md to design-system-folder-structure.md and updated its links
- fixed: renamed source-design-system-reference-bem.md to design-system-bem.md and updated its links
- fixed: renamed source-design-system-reference-tokens.md to design-system-tokens.md and updated its links
- fixed: renamed source-design-system-reference-globals.md to design-system-globals.md and updated its links
- fixed: renamed source-design-system-reference-layout.md to design-system-layout.md and updated its links

## [2026-11-16] lint | 0 fixed, 9 reported

- fixed: none
- reported: 7 findings that need a decision and 2 suggestions, see the lint report

## [2026-11-16] lint | 7 fixed, 2 reported

- fixed: design-system-tokens.md: removed the resolved Conflict: line on modifiers and design tokens
- fixed: implementation-plan.md: Stage 3 column modifiers change private state, not design tokens
- fixed: implementation-plan.md: Stage 5 --dragging modifier changes private state, not design tokens
- fixed: architecture.md: value flow has seven steps, not five
- fixed: implementation-plan.md: Stylelint prefixes are c-/l-/g-- in Stage 0 and Stage 1
- fixed: implementation-plan.md: added $margins to the Stage 1 system maps
- fixed: design-system.md: _g--color.scss takes its values from the system colour tokens

## [2026-11-16] lint | 5 fixed, 0 reported

- fixed: design-system.md: added the ## Sources section
- fixed: architecture.md: added the ## Sources section, linked testing.md
- fixed: technology.md: added the ## Sources section, linked testing.md
- fixed: implementation-plan.md: added the ## Sources section
- created: testing.md (gathers testing statements from product.md, technology.md, architecture.md, data-format.md and implementation-plan.md)
- updated: index.md
