# Design-system reference: global modifiers

Source: page 15, "Sistema de diseño: Globales", of the reference course series on design systems. Licence CC BY-SA 4.0.
Fetched: 2026-11-14. Page last modified 2026-10-25. Prose translated; names and code kept as on the page.

## What a global is

Global modifiers (prefix `g--`) are classes that can be used with any component and change **only one thing**, using the system tokens. The page also calls them utility classes. They replace inline `style` (tedious, cannot be responsive):

```html
<button boton class="g--font-size-5 g--padding-4">Aceptar</button>
```

```css
.g--font-size-5 {
  font-size: var(--mlt-sys-font-size-5) !important;
}
.g--padding-4 {
  padding: var(--mlt-sys-padding-4) !important;
}
```

Combining small single-purpose classes gives versatility, but they must be used without excess. Tailwind CSS and Bootstrap's utility API are cited as examples of the idea.

## Naming and the set on the page

Pattern `g--<property>-<N>`, directional `g--<property>-<side>-<N>`, and `g--<property>-horizontal-<N>` / `-vertical-<N>`. The value is always the token `--mlt-sys-<property>-<N>`. Every global carries `!important`.

- `g--font-size-1…11`.
- `g--margin-0…10`, `g--margin-horizontal-N`, `g--margin-vertical-N`, `g--margin-top|bottom|left|right-N`.
- `g--padding-*`: the same set.
- Colour (exercise): `g--color-<name>-<tone>`, `g--background-color-<name>-<tone>`, `g--border-color-<name>-<tone>` for every palette and semantic colour.
- Font family (exercise): `g--font-sans-serif|serif|monospace`.
- Width (exercise): `width`, `min-width`, `max-width` globals, plus one for `overflow: auto`.

## Generated with Sass

```scss
@use "sass:list";

$font-sizes: (10px, 12px, 14px, 16px, 18px, 20px, 22px, 24px, 26px, 28px, 30px);
$margins: (0px, 4px, 8px, 12px, 16px, 20px, 24px, 28px, 32px, 36px, 40px);

@for $i from 1 through list.length($font-sizes) {
  .g--font-size-#{$i} {
    font-size: var(--mlt-sys-font-size-#{$i}) !important;
  }
}

@for $i from 0 through list.length($margins) - 1 {
  .g--margin-#{$i} {
    margin: var(--mlt-sys-margin-#{$i}) !important;
  }
  .g--margin-horizontal-#{$i} {
    margin-left: var(--mlt-sys-margin-#{$i}) !important;
    margin-right: var(--mlt-sys-margin-#{$i}) !important;
  }
  .g--margin-vertical-#{$i} {
    margin-top: var(--mlt-sys-margin-#{$i}) !important;
    margin-bottom: var(--mlt-sys-margin-#{$i}) !important;
  }

  @each $lado in (top, bottom, left, right) {
    .g--margin-#{$lado}-#{$i} {
      margin-#{$lado}: var(--mlt-sys-margin-#{$i}) !important;
    }
  }
}
```

Font sizes start at 1; margins and paddings start at 0, so their loop runs to length minus one. Paddings are generated like margins.

## Responsive globals

For each breakpoint the same class exists with a suffix, inside a `min-width` media query. Breakpoints and suffixes on the page: `@tablet` 576px, `@desktop` 768px, `@fulldesktop` 992px. The `@` is escaped in the selector (`\@`), not in the HTML class attribute.

```html
<p class="g--font-size-4 g--font-size-8@fulldesktop">Hola mundo</p>
```

```scss
$breakpoints: (
  'tablet': 576px,
  'desktop': 768px,
  'fulldesktop': 992px
);

@each $nombre, $ancho in $breakpoints {
  @media (min-width: $ancho) {
    @for $i from 1 through list.length($font-sizes) {
      .g--font-size-#{$i}\@#{$nombre} {
        font-size: var(--mlt-sys-font-size-#{$i}) !important;
      }
    }
  }
}
```

Rules:

- Responsive classes also carry `!important` and must come **after** the base classes. Because the base already has `!important`, if the responsive class lacks it or comes first, the base always wins and the `@fulldesktop` class does nothing.
- The map order (ascending min-width) gives the order between breakpoints. To add a breakpoint, add it to the map.
- The suffixes apply to global modifiers and to block modifiers alike.

## Responsive techniques summary

| | Internal (the component decides) | External (the user of the component decides) |
|---|---|---|
| Fluid | `vw` or `clamp()` in the component; scrolling tables | Width globals such as `g--min-width-150 g--max-width-200` (rarely used) |
| Stepped | Media queries inside the component (e.g. a hamburger menu) | `g--font-size-4 g--font-size-8@tablet` (the most common, used by Tailwind and Bootstrap) |

Not on the page: a rule for when to prefer a block modifier over a global.
