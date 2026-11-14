# Design-system reference: tokens

Source: page 14, "Sistema de diseño: Tokens", of the reference course series on design systems. Licence CC BY-SA 4.0.
Fetched: 2026-11-14. Page last modified 2026-10-27. Prose translated; names and code kept as on the page. The course prefix `mlt` stands for the school; `sys` marks system-wide tokens (like `--mat-sys-*` in Angular Material).

## Kinds of custom properties

1. **System tokens** (`--mlt-sys-*`): global CSS variables used by every component, declared on `:root`.
   - Palette colours, named for what they are and holding the value: `--mlt-sys-color-<gama>-<1..9>` (gris, blanco, rojo, naranja, verde, azul, amarillo). Tone 1 darkest, 9 lightest.
   - Semantic colours, named for what they are used for and holding no value of their own: `--mlt-sys-color-principal-<n>`, `-alternativo-<n>`, `-peligroso-<n>`. Each tone points at the same tone of a palette colour.
   - Fonts `--mlt-sys-font-sans-serif|monospace|serif`, `--mlt-sys-font-default` (alias of sans-serif). Sizes `--mlt-sys-font-size-1…11`. Spacing `--mlt-sys-margin-0…10`, `--mlt-sys-padding-0…10`. Radius `--mlt-sys-border-radius-1…4`. Widths `--mlt-sys-width-0…11`.
   - Components use the semantic colours, never the palette: the button is `principal`, not `azul`. If tomorrow `principal` is green, the nine `principal` lines change and every component follows.
2. **Design tokens** (`--<bloque>-*`, prefix is the block name without `c-`): the public variables of a component, a designer's decision. Each has **one single value**, by default a system token. Pattern `--<bloque>-<propiedad>[-<función|tamaño|vertical/horizontal>]`: `--boton-color-normal`, `--boton-font-size-s`, `--boton-padding-vertical-m`, `--boton-border-radius`.
3. **Private state** (`--<bloque>-privateState-*`, camelCase kept, names are case-sensitive): not tokens but the CSS needed to implement the block, of no interest to the designer. Its value depends on the modifiers. Only the block's own modifiers change it. Test: if the value depends on the modifier the block carries, it is state.
4. **CSS properties**: only use the state and the tokens.

| | Design token | Private state |
|---|---|---|
| Holds | A designer's decision | How the block is right now |
| Name | `--boton-*` | `--boton-privateState-*` |
| Value | One, by default a system token | Depends on the modifiers |
| Who changes it | The designer or whoever customises the block | Only the block's modifiers |

`transparent` is a CSS keyword, not a token.

## Reference chain

`--mlt-sys-color-azul-5` → `--mlt-sys-color-principal-5` → `--boton-color-normal` → `--boton-privateState-color` → `background-color` / `border-color` / `color`.

- Design tokens default to system tokens.
- Private state points at design tokens or at other private state of the same block.
- Properties point at private state or design tokens, never at system tokens directly.

## System tokens generated with Sass

Values live in Sass lists and maps; tokens are generated with loops so nothing is typed by hand.

```scss
@use "sass:list";
@use "sass:map";
@use "sass:meta";

$colores: (
  'gris': (#171717, #262626, #404040, #525252, #737373, #A1A1A1, #D4D4D4, #E5E5E5, #F5F5F5),
  'azul': (#1C398E, #193CB8, #1447E6, #155DFC, #2B7FFF, #51A2FF, #8EC5FF, #BEDBFF, #DBEAFE),
  // rojo, naranja, verde, amarillo, blanco likewise
);

$colores-semanticos: (
  'principal': 'azul',
  'alternativo': 'naranja',
  'peligroso': 'rojo'
);

$fuentes: (
  'sans-serif': (-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif),
  'monospace': (SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace),
  'serif': (Palatino, "Palatino Linotype", "Book Antiqua", Georgia, "Times New Roman", Times, serif)
);

$font-sizes: (10px, 12px, 14px, 16px, 18px, 20px, 22px, 24px, 26px, 28px, 30px);
$paddings: (0px, 4px, 8px, 12px, 16px, 20px, 24px, 28px, 32px, 36px, 40px);
$border-radius: (1px, 2px, 4px, 6px);
$widths: (0px, 1px, 4px, 8px, 12px, 16px, 20px, 24px, 28px, 32px, 36px, 40px);

:root {
  @each $nombre, $tonos in $colores {
    @for $i from 1 through list.length($tonos) {
      --mlt-sys-color-#{$nombre}-#{$i}: #{list.nth($tonos, $i)};
    }
  }

  // Cada tono de un color semántico apunta al mismo tono de su gama
  @each $nombre, $gama in $colores-semanticos {
    @for $i from 1 through list.length(map.get($colores, $gama)) {
      --mlt-sys-color-#{$nombre}-#{$i}: var(--mlt-sys-color-#{$gama}-#{$i});
    }
  }

  @each $nombre, $fuente in $fuentes {
    --mlt-sys-font-#{$nombre}: #{meta.inspect($fuente)};
  }
  --mlt-sys-font-default: var(--mlt-sys-font-sans-serif);

  @for $i from 1 through list.length($font-sizes) {
    --mlt-sys-font-size-#{$i}: #{list.nth($font-sizes, $i)};
  }

  // Los paddings y los anchos empiezan en 0
  @for $i from 0 through list.length($paddings) - 1 {
    --mlt-sys-padding-#{$i}: #{list.nth($paddings, $i + 1)};
  }

  @for $i from 1 through list.length($border-radius) {
    --mlt-sys-border-radius-#{$i}: #{list.nth($border-radius, $i)};
  }

  @for $i from 0 through list.length($widths) - 1 {
    --mlt-sys-width-#{$i}: #{list.nth($widths, $i + 1)};
  }
}
```

Margins are generated like paddings (exercise 1 computes both from `$paso: 4px` and `$numero-pasos: 10`).

## The button, final form

Modifiers in three independent groups: **función** (`--funcion-normal|alternativa|peligrosa`) chooses the colour; **importancia** (`--importancia-primaria|secundaria|terciaria`) decides whether the colour goes on the background, the border or the text; **size** (`--size-s|m|l`) chooses font size and paddings. `:hover` is one more modifier, set by the browser.

```scss
.c-boton {
  // Tokens de diseño: decisiones del diseñador, un valor cada uno
  --boton-color-normal: var(--mlt-sys-color-principal-5);
  --boton-color-alternativa: var(--mlt-sys-color-alternativo-5);
  --boton-color-peligrosa: var(--mlt-sys-color-peligroso-5);
  --boton-color-normal-hover: var(--mlt-sys-color-principal-4);
  --boton-color-alternativa-hover: var(--mlt-sys-color-alternativo-4);
  --boton-color-peligrosa-hover: var(--mlt-sys-color-peligroso-4);
  --boton-color-texto-primaria: var(--mlt-sys-color-blanco-9);

  --boton-font-family: var(--mlt-sys-font-default);
  --boton-font-size-s: var(--mlt-sys-font-size-3);
  --boton-font-size-m: var(--mlt-sys-font-size-4);
  --boton-font-size-l: var(--mlt-sys-font-size-6);
  --boton-padding-vertical-s: var(--mlt-sys-padding-1);
  --boton-padding-vertical-m: var(--mlt-sys-padding-2);
  --boton-padding-vertical-l: var(--mlt-sys-padding-3);
  --boton-padding-horizontal-s: var(--mlt-sys-padding-3);
  --boton-padding-horizontal-m: var(--mlt-sys-padding-4);
  --boton-padding-horizontal-l: var(--mlt-sys-padding-6);
  --boton-border-width: var(--mlt-sys-width-1);
  --boton-border-radius: var(--mlt-sys-border-radius-4);

  // Estado privado: por defecto, normal, primaria, tamaño m y sin hover
  --boton-privateState-color: var(--boton-color-normal);
  --boton-privateState-color-hover: var(--boton-color-normal-hover);
  --boton-privateState-background-color: var(--boton-privateState-color);
  --boton-privateState-border-color: var(--boton-privateState-color);
  --boton-privateState-text-color: var(--boton-color-texto-primaria);
  --boton-privateState-font-size: var(--boton-font-size-m);
  --boton-privateState-padding-vertical: var(--boton-padding-vertical-m);
  --boton-privateState-padding-horizontal: var(--boton-padding-horizontal-m);

  // Propiedades CSS: solo usan el estado y los tokens
  font-family: var(--boton-font-family);
  font-size: var(--boton-privateState-font-size);
  padding: var(--boton-privateState-padding-vertical) var(--boton-privateState-padding-horizontal);

  border-radius: var(--boton-border-radius);
  border-width: var(--boton-border-width);
  border-style: solid;

  display: inline-block;
  cursor: pointer;
  text-decoration: none;

  border-color: var(--boton-privateState-border-color);
  background-color: var(--boton-privateState-background-color);
  color: var(--boton-privateState-text-color);

  // La función elige qué tokens de color se usan
  &--funcion-normal {
    --boton-privateState-color: var(--boton-color-normal);
    --boton-privateState-color-hover: var(--boton-color-normal-hover);
  }
  &--funcion-alternativa {
    --boton-privateState-color: var(--boton-color-alternativa);
    --boton-privateState-color-hover: var(--boton-color-alternativa-hover);
  }
  &--funcion-peligrosa {
    --boton-privateState-color: var(--boton-color-peligrosa);
    --boton-privateState-color-hover: var(--boton-color-peligrosa-hover);
  }

  // La importancia decide dónde va el color: fondo, borde o texto
  &--importancia-primaria {
    --boton-privateState-background-color: var(--boton-privateState-color);
    --boton-privateState-border-color: var(--boton-privateState-color);
    --boton-privateState-text-color: var(--boton-color-texto-primaria);
  }
  &--importancia-secundaria {
    --boton-privateState-background-color: transparent;
    --boton-privateState-border-color: var(--boton-privateState-color);
    --boton-privateState-text-color: var(--boton-privateState-color);
  }
  &--importancia-terciaria {
    --boton-privateState-background-color: transparent;
    --boton-privateState-border-color: transparent;
    --boton-privateState-text-color: var(--boton-privateState-color);
  }

  // El tamaño elige qué tokens de font-size y padding se usan
  &--size-s {
    --boton-privateState-font-size: var(--boton-font-size-s);
    --boton-privateState-padding-vertical: var(--boton-padding-vertical-s);
    --boton-privateState-padding-horizontal: var(--boton-padding-horizontal-s);
  }
  &--size-m { /* m tokens */ }
  &--size-l { /* l tokens */ }

  // Al pasar el ratón, se usa el color de hover de su función
  &:hover {
    --boton-privateState-color: var(--boton-privateState-color-hover);
  }
}
```

```html
<button class="c-boton c-boton--funcion-peligrosa c-boton--importancia-secundaria c-boton--size-s">Borrar</button>
```

## Rules stated on the page

- A token has one single value: three colours means three tokens (`--boton-color-normal`, `-alternativa`, `-peligrosa`), not one token with three values.
- Modifiers **always** change the state, never the CSS properties and never the tokens. `.c-boton--size-l { font-size: var(--boton-font-size-l) }` works but breaks the rule.
- Each new modifier group is one more group of private state, so modifiers combine freely: a button can be dangerous, secondary and small at once.
- The transparent border in `terciaria` keeps the size stable; that is a CSS trick, not a design decision.
- Never redefine private state from outside. To make danger buttons another red, change `--boton-color-peligrosa`. To customise a button in a context, redefine its design tokens: `.c-aviso .c-boton { --boton-color-normal: var(--mlt-sys-color-peligroso-5); }`.
- `:hover` cannot name a function's colour directly (every button would turn `normal` on hover); the function modifier leaves the matching hover colour ready in the state.
- The hover colour is chosen from the palette (tone 4 instead of 5); no new system tokens are needed.

## Theming

No dark mode on the page. Two levers: redefine a block's design tokens in a context selector (component level), or change the Sass maps (`$colores-semanticos`, e.g. `'principal': 'verde'`) and recompile (system level).

## Comparison with Angular Material

| Angular Material | This system |
|---|---|
| System token `--mat-sys-primary` | `--mlt-sys-color-principal-5` |
| Design token `--mat-button-filled-container-color` | `--boton-color-normal` |
| Design token takes a system token: `var(--mat-button-filled-container-color, var(--mat-sys-primary))` | `--boton-color-normal: var(--mlt-sys-color-principal-5)` |
| Change a component: `mat.button-overrides(…)` | redefine its `--boton-*` design tokens (never the `privateState`) |
| Change the whole system: `mat.theme` | change the Sass variables that generate `--mlt-sys-*` |
| Variants `matButton="filled" | "outlined" | "text"` | modifiers `--importancia-primaria | -secundaria | -terciaria` |
