# Serialization

Domeleon's component tree is fully serializable, facilliating hot-reload via persisting to local storage, and transfering objects to and from the server. How it works:

* Every `Component` has a `serializer`.
* `serialize()` walks your component tree and produces plain JSON objects.
* `deserialize(json)` deserializes a json object into your existing component instance (recursing into children).

When an `App` is started with `autoPersist: true`, domeleon automatically saves after each `update`, and restores on load via local storage. It's trivial to set up:

```ts
import { App } from 'domeleon'

const app = new App({
  rootComponent : new Master(),
  containerId : 'app',
  autoPersist : true // save & restore automatically
})
```

## Component Serialization

We can individually serialize or deserialize a component as follows:

```ts
// turn any component into a JSON object
const snapshot = this.serializer.serialize()

// …later – deserialize the JSON object into our component
this.serializer.deserialize(snapshot)
```
The serializer only scan properties that are:

* read *and* write
* primitives, objects, or classes

And not:

* functions
* read only properties

You can get the exact set of properties that your component will serialize by calling your component context's `dataKeys` property:

```ts
const keys = this.ctx.dataKeys
```

## Deserializing classes (and dates)

Declare a static `types` map enables plain json objects to be deserialized from plain objects to classes:

```ts
export class Tree extends Component {
  static types = {           // <prop>: <ctor | [ctor]>
    trees  : [Tree],         // array of components
    created: Date            // non-component class
  }

  trees: Tree[] = []
  created = new Date()
}
```
The types must either be classes, with the one exception being `Date`. This is necessary because Javascript doesn't actually store runtime type information per properties; that's a TypeScript only concept that is destroyed in transpilation.

> **Note**: Domeleon's serializer does **not** use decorators for this purpose, as the latest spec is still not natively implemented by browsers as of 2025.

## Handling Sets and Maps

For simplicity, Domeleon only allows nested components to be single instance values or arrays. This makes Domeleon easy to reason about, as well as providing a direct correspondence with our component tree and the JSON format.

We can however, still use Maps. We just need to make sure our map is:

1. Private
2. If public, only exposed via a read-only property, or method.
3. If serialized, expose publcally as an array.

This is a real-world example of how we handle the `collapsed` state:

```ts
export class InspectorComponentTree extends Component {
  #collapseMap = new Map<string, boolean>()

  get collapseState () {    
    return Array.from (
      this.#collapseMap.entries()).map(([id, collapsed]) => ({ id, collapsed })
    )
  }

  set collapseState (state: { id: string; collapsed: boolean }[]) {
    this.#collapseMap = new Map(
      state.map(({ id, collapsed }) => [id, collapsed])
    )
  }
}
```
This means we get the performance benefits of a `Map`, but can fully partake in the domeleon object model.

## Related

* [App](./App)
* [Components](./components.md)