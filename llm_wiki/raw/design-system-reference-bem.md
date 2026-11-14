# Design-system reference: BEM

Source: page 13, "Sistema de diseño: BEM", of the reference course series on design systems. Licence CC BY-SA 4.0.
Fetched: 2026-11-14. Page last modified 2026-11-02. Prose translated; class names and code kept as on the page.

## CSS architecture goals

Predictable, reusable, maintainable, scalable. Agree the rules as a team, explain the base structure to new members, avoid very long stylesheets, document the non-obvious parts. The course adopts BEM (getbem.com).

## Blocks and elements

- First identify the blocks of the page: they are the components or objects of the page. A block may contain other blocks.
- A block may have elements. The element class is the block name, `__`, the element name. The `__` is required; nothing else may be used.
- A block's class carries the prefix `c-` when it is a visual component.

```html
<div class="c-panel">
    <div class="c-panel__titulo">Cabecera</div>
    <div class="c-panel__contenido">
        <p>Contenido</p>
        <p>Mas contenido</p>
    </div>
</div>
```

The `<p>` tags are not part of the panel: they are another block placed inside it. A button is a block with no elements: `<button class="c-boton">Mas información</button>`.

## Modifiers

A modifier is a new class named after the block or element plus `--` and the modifier name.

```html
<div class="c-panel c-panel--warning">
    <div class="c-panel__titulo c-panel__titulo--warning">Cabecera</div>
    <div class="c-panel__contenido c-panel__contenido--warning">
        <p>Contenido</p>
    </div>
</div>
```

Rules:

- Each tag carries at most one block class or one element class.
- An element class is always on a tag inside a tag that carries its block class.
- A block or element may carry more classes only if they are modifiers of that same block or element.

## Prefixes

- `c-`: blocks that are visual components (panels, buttons, trees…).
- `l-`: layout blocks. A layout block only says how the blocks inside it are placed. A visual component also organises its elements, but it is not dedicated only to placing things.
- `g--`: global modifier (letter g and two hyphens), usable with any component, usually for margins and paddings, so each component does not need its own copy. Examples: `g--margin-4`, `g--margin-bottom-6`.

The page defines no other prefixes.

```html
<body class="c-body">
    <div class="l-page">
        <div class="l-page__area-header">
            <img class="c-logo">
            <div class="c-menu">
                <div class="c-menu__item">Nuevo</div>
                <div class="c-menu__item c-menu__item--active">Editar</div>
                <div class="c-menu__item c-menu__item--disable">Borrar</div>
            </div>
        </div>
        <div class="l-page__area-body">
            <form class="c-form g--margin-4 g--margin-bottom-6">
                <input class="c-input">
                <button class="c-boton c-boton--funcion-normal c-boton--importancia-primaria">Guardar</button>
            </form>
        </div>
        <div class="l-page__area-footer">
            <div class="l-horizontal">
                <img class="c-social" src="facebook.png">
            </div>
        </div>
    </div>
</body>
```

## BEM with Sass

One Sass file per block, holding a single `.c-xxx { }` with all its elements and modifiers nested with the parent selector `&`, so the block name is written once:

```scss
.c-panel {
  border: 1px solid #c0c0c0;
  border-radius: 6px;

  &--warning {
    border-color: #ed8936;
  }

  &__titulo {
    padding: 8px;
    font-weight: bold;
    color: #ffffff;
    background-color: #0056b8;

    &--warning {
      background-color: #ed8936;
    }
  }

  &__contenido {
    padding: 8px;

    &--warning {
      background-color: #fef5e6;
    }
  }
}
```

## Dangers

**Naming the content.** `c-ventas__euros` and `c-ventas__mes` lie as soon as the block shows customers instead of sales, and `c-clientes` would duplicate the CSS. Name what the thing is: `c-dato__valor`, `c-dato__etiqueta`. Test: mentally swap the text for other text; if the class stops making sense, it names the content, not the component.

**Two elements that are the same.** `c-ficha__nombre` and `c-ficha__apellidos` with identical font and size and a different colour duplicate CSS. They are one element with a modifier: `c-ficha__titulo` and `c-ficha__titulo c-ficha__titulo--secundario`, where the modifier changes only the colour. Rule: if two elements share almost all their CSS, they are the same element with a modifier.

Not on the page: specificity, any framework or Angular rule.
