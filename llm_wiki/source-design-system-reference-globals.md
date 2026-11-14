---
description: Summary of the reference course page on global modifiers: g-- utility classes that change one property from a system token with !important, generated with Sass loops, and their responsive @breakpoint suffixes. Read when you add a g-- class or need a one-off spacing or size in a template.
---

# Source: design-system reference, global modifiers

What it is: page 15 of the reference course series on design systems (CC BY-SA 4.0). Page last modified 2026-10-25; filed in [raw/design-system-reference-globals.md](raw/design-system-reference-globals.md) on 2026-11-14.

Statements that matter for the project:

1. A global modifier (prefix `g--`) is a class usable with any component that changes **one thing only**, using a system token. It replaces inline `style`, which cannot be responsive.
2. Pattern `g--<property>-<N>`, with `-<side>-`, `-horizontal-` and `-vertical-` variants; the value is `var(--<prefix>-sys-<property>-<N>)`. Every global carries `!important`.
3. The set on the page: font sizes, margins, paddings; exercises add colours, font families and widths.
4. Globals are generated with `@for` / `@each` from the same Sass lists as the system tokens, so the class and the token always match.
5. Responsive variants add a suffix per breakpoint (`@tablet` 576px, `@desktop` 768px, `@fulldesktop` 992px on the page), generated from a `$breakpoints` map inside `min-width` media queries. The `@` is escaped as `\@` in the selector, not in the HTML.
6. Responsive classes must come after the base classes and also carry `!important`; placed before, the base always wins.
7. The suffix convention applies to block modifiers too, not only globals.
8. Use globals without excess. The page gives no rule for choosing a block modifier over a global.

Pages changed by this source: [design-system.md](design-system.md#global-modifiers).
