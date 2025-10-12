# Components

Derive from the `Component` class to encapsulate part of your domain model. Then build out a tree of components to structure your application. Here, we build upon our introductory example, by composing two smaller components into a new component:

```ts
import { Component, div } from 'domeleon'
import { Counter } from './counter'

export class TwinCounters extends Component {
  counter1 = new Counter()
  counter2 = new Counter()

  view() {
    return div(
      this.counter1.view(), // Call child's view method
      this.counter2.view()
    )
  }
}
```
>💡**Component != DOM Element**  
> A component encapsulates a piece of application state, **not** a DOM element.

You can also specify arrays of components. Here's the structure of a recursive tree component:

```ts
import { Component } from 'domeleon'

export class Tree extends Component
{    
  trees: Tree[] = []
  ...
}
```
Let's flesh this out:

* add and remove components
* enable serialization so it roundtrips & our component state hot-reloads
* add a visualisation of each node.

```ts
import { Component, button, div, VElement, SerializerMap } from 'domeleon'

export class Tree extends SampleComponent {
  serializerMap: SerializerMap<SampleComponent> = { trees: [Tree] } // optional, for derialization
  trees: Tree[] = []

  add() {
    this.trees.push(new Tree())
    this.update()
  }

  remove() {
    this.trees.pop()
    this.update()
  }

  view(): VElement {
    return div({ style: { paddingLeft: `${this.ctx.rootToHere.length * 10}px` } },
      this.ctx.componentId,
      button({ onClick: () => this.add() }, "add"),
      this.trees.length ?
        button({ onClick: () => this.remove() }, "remove") :
        undefined,
      this.trees.map(c => c.view())
    )
  }
}
```

The `serializerMap` field enables our tree's children to be successfully deserialized. It's a simple map to deserialize the raw incoming json objects into classes.

> **💡Deep Dive**: Every component is assigned an auto-incrementing unique `componentId` at construction, accessible via its `ctx` property. It's used by `formField` to automatically assign labels to inputs with unique ids, so you never need to. You rarely actually use it explicitly like we did in this example, but it's informative in understanding how domeleon works.

# Component Initialization

## Construction
Your component is simply a class instance, so its life begins with a constructor call.

## `onAttached`

The `onAttached` method is called after your component has its `ctx.app` and `ctx.parent` property set.

```ts
   override onAttached () {
       // Perform setup, e.g.: // start subscriptions, fetch data
   }
```

The `onAttached` method is **guaranteed** to be called:

* once per component attached to the component tree
* *synchronously*, after an update (or app start) made it accessible from the root component
* *before* the next render
* *after every* component in the component tree for that update (or app start) has had its `parent` and `child` properties set
* before `onAttached` is called on its children

>**Tip**: The `onNavigate` method is often used in tandem with `onAttached` to build out your component model. It's particulary useful for asynchronously fetching data from the server upon navigation.

# Updates

The primary function of `update` is to trigger a re-render of the `app`. As we saw earlier:

```ts
  add (x: number) {
    this.count += x
    this.update()
  }
```
Specifically, `update` triggers:

* `onUpdated` on the component and each ancestor up to the root
* a save, if `autoPersist` is enabled
* a re-render of the app

By far the most expensive part of on update cycle is changes to the *DOM*. Since Domeleon runs atop state-of-the-art virtual DOMs, these operations are extremely fast and reliable.

>**Tip:** Use the domeleon inspector to live view all updates to your component tree. It's helps understand the model.

>**Tip:** In certain exceptional circumstances, such as a complex custom animations, postpone calling `update`. This is exactly how the Mandelbrot Explorer written with Domeleon works while zooming, as each calculation needs to run within 16ms! Domeleon lets you be pragmatic.

# Views

Views are pure functions of component state: each component's view returns an extremely light-weight immutable `VElement` tree.

The app's chosen `renderer` takes this tree and translates it into the specific VDOM nodes (e.g., Preact, Vue, or React nodes) used by the underlying renderer, which then patches the VDOM.

`view` methods tend to be parameterless, as components are natural units of encapsulation: they simply render their own state. However, the world isn't perfect! In domeleon, you can add optional parameters to your child components' `view` methods. This can be useful when a child's view needs to render state that makes more sense to live on the parent

Critically, domeleon is model-centric, not view-centric. A domeleon component represents a piece of state, not a piece of HTML. A component might be viewless or have multiple methods returning different `VElement`s. For example:

```ts
  summaryView () {
    return div (...)
  }

  detailView () {
   return div (...)
  }
```
## Related Topics

* [Forms](./forms.md)