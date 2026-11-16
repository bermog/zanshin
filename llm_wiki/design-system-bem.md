---
description: Summary of the reference course page on BEM: block, element and modifier naming, the c-/l-/g-- prefixes, the one-class-per-tag rules, one Sass file per block, and the two naming dangers. Read when you name a CSS class or review a block's structure.
---

# Source: design-system reference, BEM

What it is: page 13 of the reference course series on design systems (CC BY-SA 4.0). Page last modified 2026-11-02; filed in [raw/design-system-reference-bem.md](raw/design-system-reference-bem.md) on 2026-11-14.

Statements that matter for the project:

1. Element class = block name + `__` + element name; the `__` is required. Modifier class = block or element class + `--` + modifier name.
2. Each tag carries at most one block class or one element class. An element's tag is always inside its block's tag. Extra classes on a tag may only be modifiers of that same block or element.
3. Prefixes: `c-` for visual components, `l-` for layout blocks (they only place other blocks), `g--` for global modifiers usable with any component (typically margins and paddings). No other prefixes exist.
4. A block may contain other blocks; content inside a block that is not part of it (a `<p>` in a panel) is another block, not an element.
5. One Sass file per block, holding a single `.c-xxx { }` with every element and modifier nested with `&`, so the block name is written once.
6. Do not name the content: a class must still make sense when the text changes (`c-dato__valor`, not `c-ventas__euros`).
7. If two elements share almost all their CSS, they are one element with a modifier that changes only the difference.
8. Not covered: specificity, frameworks.

Pages changed by this source: [design-system.md](design-system.md#naming-bem), [architecture.md](architecture.md#naming-conventions).
