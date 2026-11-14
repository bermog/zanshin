# Design-system reference: layout blocks

Source: page 16, "Sistema de diseño: Layout", of the reference course series on design systems. Licence CC BY-SA 4.0.
Fetched: 2026-11-14. Page last modified 2026-10-24. Prose translated; names and code kept as on the page.

## Rules

- Layout blocks use BEM with the prefix `l-`.
- Always two elements: the parent `div` (the block) and the child `div`s, called **areas** (grid terminology). Area classes are `__area` or `__area-<name>`.
- The CSS only contains what changes the position (and sometimes the size) of the areas. It never changes their colours, fonts, etc.
- Putting the area class on the children is optional when it has no use.
- An area may hold a component, because their CSS properties do not clash. The component may carry the area class itself.
- An area may hold another layout block.

Not on the page: media queries, breakpoints, tokens, globals, padding or gap rules.

## Layouts defined

### l-flex

```html
<div class="l-flex l-flex--direction-row l-flex--justify-content-center">
  <div class="l-flex__area">Item1</div>
  <div class="l-flex__area">Item2</div>
  <div class="l-flex__area l-flex__area--grow2">Item3</div>
</div>
```

```scss
.l-flex {
  display: flex;

  &--direction-row {
    flex-direction: row;
  }

  &--justify-content-center {
    justify-content: center;
  }

  &__area {
    &--grow2 {
      flex-grow: 2;
    }
  }
}
```

### l-columns

Grid by columns; the 1-to-12 modifiers are generated with a loop. An area spans two columns with `l-columns__area--span2`.

```html
<div class="l-columns l-columns--4">
  <div class="l-columns__area"></div>
  <div class="l-columns__area l-columns__area--span2"></div>
  <div class="l-columns__area"></div>
</div>
```

```scss
.l-columns {
  display: grid;
  grid-template-columns: repeat(1, 1fr);

  @for $i from 1 through 12 {
    &--#{$i} {
      grid-template-columns: repeat($i, 1fr);
    }
  }

  &__area {
    &--span2 {
      grid-column: span 2;
    }
  }
}
```

### l-extremo

Pushes two areas to opposite ends with auto margins.

```html
<div class="l-extremo">
    <button class="l-extremo__area-izquierda c-boton">Pulsar Izquierda</button>
    <button class="l-extremo__area-derecha c-boton">Pulsar Derecha</button>
</div>
```

```scss
.l-extremo {
  display: flex;

  &__area-izquierda {
    margin-right: auto;
  }

  &__area-derecha {
    margin-left: auto;
  }
}
```

## Exercise

Build a `landing-page` layout with the areas `cabecera`, `lateral-izquierdo`, `cuerpo`, `lateral-derecho`, `pie` (`<name>__area-<area>`). No solution given.
