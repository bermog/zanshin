# Index

The catalog of the wiki. Each line links to a page and copies the page's `description`. Read this file first, then read each page whose description matches the task. Before you change a wiki file, read the schema in [AGENTS.md](AGENTS.md).

## Project

- [product.md](product.md): The purpose, the target users, the key features and the business objectives of the product. Read when a task depends on what the product is for or who uses it.
- [technology.md](technology.md): The frameworks, the libraries, the commands for build, test, lint and run, deployment, and the technical constraints. Read when you add a dependency, run a command or choose a tool.
- [architecture.md](architecture.md): The file organization, the naming conventions, the import patterns and the architectural decisions. Read when you add, move or rename a file, or change how parts of the code depend on each other.
- [implementation-plan.md](implementation-plan.md): The stages in which v1 is built, what each stage delivers and how to tell a stage is done. Read when you start a stage, check what is already built or decide what to build next.
- [roadmap.md](roadmap.md): The product direction over time as releases, from the first working build to ideas beyond v1. Read when you decide whether a feature belongs in the current release or a later one.
- [design-system.md](design-system.md): The styling system in one place: Sass layers, system and semantic variables and tokens, per-block design tokens and private state, BEM naming, global modifiers, layout blocks, Stylelint rules and the visual direction. Read when you write or review any SCSS, add a c-/l-/g-- block or class, or change a colour, size or theme.
- [accessibility.md](accessibility.md): The accessibility requirements in one place: WCAG 2.2 AA, keyboard alternatives for every action including card moves, focus management, live-region announcements, contrast and reduced motion. Read when you build or review any interaction, dialog, form or animation.
- [data-format.md](data-format.md): The board data in one place: the BoardState shape, the export envelope, schemaVersion and migrations, import validation and confirmation, and how each storage failure is handled. Read when you touch models, BoardService, BoardStorageService, BoardTransferService, the import validator or anything that reads or writes the board.

## Sources

- [source-design-system-reference-folder-structure.md](source-design-system-reference-folder-structure.md): Summary of the reference course page on the folder structure of a Sass design system (six sNN layers, their contents, load order and the utilities import), which Zanshin's src/styles/ follows. Read when you need to know what the reference structure prescribes and where Zanshin deviates from it.
- [source-design-system-reference-bem.md](source-design-system-reference-bem.md): Summary of the reference course page on BEM: block, element and modifier naming, the c-/l-/g-- prefixes, the one-class-per-tag rules, one Sass file per block, and the two naming dangers. Read when you name a CSS class or review a block's structure.
- [source-design-system-reference-tokens.md](source-design-system-reference-tokens.md): Summary of the reference course page on tokens: system tokens generated from Sass maps, per-block design tokens with one value each, private state that only modifiers change, and properties that read only state and tokens. Read when you write a c- block's custom properties or a modifier.
- [source-design-system-reference-globals.md](source-design-system-reference-globals.md): Summary of the reference course page on global modifiers: g-- utility classes that change one property from a system token with !important, generated with Sass loops, and their responsive @breakpoint suffixes. Read when you add a g-- class or need a one-off spacing or size in a template.
- [source-design-system-reference-layout.md](source-design-system-reference-layout.md): Summary of the reference course page on layout blocks: l- blocks made of a parent and named areas, CSS limited to position and size, and the l-flex, l-columns and l-extremo examples. Read when you create or review an l- block.

## Agent instructions

- [instruction-wording.md](instruction-wording.md): How to word a single rule or constraint for a model. Read when you write or edit any prompt, skill or agent instruction.
- [agent-procedures.md](agent-procedures.md): Steps that an agent runs with tools, with retry limits, done states and failure actions, and when to use a goal description instead. Read when instruction text has steps or covers a multi-step task.
- [skill-packaging.md](skill-packaging.md): The trigger description, the examples and the length of a skill or reusable prompt. Read when you create or edit a skill, a slash command or an agent definition.
- [instruction-review.md](instruction-review.md): The review checklist for agent instructions and the cases where the rules matter most. Read when you review a prompt, a skill or other instruction text.
