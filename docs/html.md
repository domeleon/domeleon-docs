# HTML Helpers

Domeleon provides HTML helper functions (`div`, `span`, `button`, etc.) that create `VElement` objects. These helpers accept attribute objects followed by a spread of child elements or primitive values.

Examples:

```ts
import { div, button } from 'domeleon'

div()                                      // empty <div></div>
div("hello")                               // <div>hello</div>
div({ id: 'my-div' })                      // <div id="my-div"></div>
div({ id: 1, class: "foo" })               // <div id="1" class="foo"></div>
div({ id: 'main' }, "hello")               // <div id="main">hello</div>
div(div({ class: 'inner' }))               // <div><div class="inner"></div></div>
div({ id: 'combo' }, "hello", div("bye"))  // <div id="combo">hello<div>bye</div></div>

button({ onClick: () => console.log('clicked') }, "Click Me") // <button>Click Me</button>
```
Internally, `div`, `button` etc. just call the `h` function, passing in the element name.

## `class` and `style`

Domeleon treats these properties specially:

* `class` may be a `string` or `string[]`
* `class` preprocesses **utility** classes (e.g. converts " " to "_")
* `style` *only* takes an *object*, not a `string`
* `class` and `style` properties are merged correctly when passing multiple attribute objects to an `h` function.

For example, the following 3 statements are all equivalent:

```ts
div({class: "x y" })
div({class: ["x", "y"] })
div({class: "x"}, { class: "y" }
```

Import `mergeAttrs` should you need to merge attribute objects outside the context of an `h` function.

## Event Handlers

Event handlers are specified directly in attribute objects:
```ts
button({ onClick: () => this.add(1) }, "+")
```

JavaScript's `this` binding requires care. Follow these patterns within Domeleon components:

1.  **Wrap callbacks in closures:** To preserve the correct `this` context.
```ts
    // CORRECT - this will guarantee the correct `this` pointer reference
    button({ onClick: e => this.handleClick(e) }, "Click")
    
    // WRONG - this can lose the *this* pointer reference
    button({ onClick: this.handleClick }, "Click")
```

Domeleon always expects `camelCase`, from events through to aria attributes. Each vdom specific renderer automatically transforms the casing appropriately (e.g. to kebab-casing etc.).

## `onMounted` callback

The vast majority of your code won't need to touch the DOM; you simply output vdom nodes, and let the renderer patch your changes automatically.

However, sometimes you need direct access to DOM elements. It might be useful for focus management, integrating 3rd-party libraries that operate on a DOM element, or drawing on a canvas.

We can use the `onMounted` event on an attribute, to obtain a reference to a DOM element:

```ts
import { Component, VElement, canvas } from 'domeleon'

export class Paint extends Component {
  view() {
    return canvas({
      width: 150,
      height: 150,
      onMounted: elm => {    
        const canvas = element as HTMLCanvasElement
      }
    })
  }
}
```
You can optional return a callback that is called when the element is unmounted.

## DOM Keys

After each update, the virtual DOM is patched. The patcher compares the current virtual DOM tree to the previous one, and modifies the real DOM accordingly. However, the patching algorithm can't know your intent, and so occassionally does the wrong thing. It may try to reuse an element that you definitely want to replace, or it may try to replace a list of child elements that you merely wanted to reorder. To better declare your intent, provide keys for your virtual DOM nodes. For example:

```ts
div ({key: wizardPage})
```
If the key changes, the patcher now knows to definitely recreate that DOM element. This means even if your next wizard page happened to have an input that could have been updated, that instead it will be replaced, predictably resetting DOM state like focus and selections, and invoking any animations that should occur on element creation.

## Related Topics

* [Unocss](./unocss)
* [Pluggable VDOM - React/Vue/Preact](./pluggable-vdom.md)