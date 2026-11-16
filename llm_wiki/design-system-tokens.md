---
description: Summary of the reference course page on tokens: system tokens generated from Sass maps, per-block design tokens with one value each, private state that only modifiers change, and properties that read only state and tokens. Read when you write a c- block's custom properties or a modifier.
---

# Source: design-system reference, tokens

What it is: page 14 of the reference course series on design systems (CC BY-SA 4.0). Page last modified 2026-10-27; filed in [raw/design-system-reference-tokens.md](raw/design-system-reference-tokens.md) on 2026-11-14. The course prefix is `--mlt-sys-*`; Zanshin uses `--zn-sys-*` and `--zn-sem-*`.

Statements that matter for the project:

1. System tokens are global custom properties on `:root`, generated with Sass loops from lists and maps (`$colores`, `$fuentes`, `$font-sizes`, `$paddings`, `$border-radius`, `$widths`). Nothing is typed by hand.
2. Semantic colours (`principal`, `alternativo`, `peligroso`) hold no value; each tone points at the same tone of a palette colour. Components use semantic colours, never the palette.
3. A block declares **design tokens** `--<block>-*` at the top: public, one single value each, by default a system token. Three colours means three tokens, not one token with three values.
4. A block declares **private state** `--<block>-privateState-*` after its tokens: the CSS needed to implement the block, whose value depends on the modifiers. Names are case-sensitive.
5. CSS properties only read private state and design tokens, never system tokens directly.
6. Modifiers **always** change private state, never CSS properties and never tokens. Each modifier group (function, importance, size) owns its own state, so modifiers combine freely.
7. `:hover` is one more modifier that only changes state; the function modifier leaves the matching hover colour ready in the state, because `:hover` cannot know the function.
8. To customise a block in a context, redefine its design tokens (`.c-aviso .c-boton { --boton-color-normal: … }`); never redefine private state from outside.
9. To retheme the whole system, change the Sass maps and recompile. The page has no dark mode.
10. `transparent` is a keyword, not a token; a transparent border that keeps the size is a CSS trick, not a design decision.

Pages changed by this source: [design-system.md](design-system.md#value-flow), [architecture.md](architecture.md#architectural-decisions).
