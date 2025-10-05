# Serialization

Domeleon's component tree is fully serializable, facilliating hot-reload via persisting to local storage, and transfering objects to and from the server.

## ctx.keys and serializer.keys properties

Domeleon only cares about your Component's *ctx.keys* properties, which are:

* **public**
  * **not** properties starting with `_`
* **read** *and* **write**
  * **not** properties with only a getter
* type is **primitive**, **object**, or **class**
  * **not** functions

You can get the exact set of properties that your component will serialize by calling your component's `serializer.keys` property:

```ts
const keys = this.serializer.keys
```
These are the same as `ctx.keys`, but further filtered by any key set to `null` in your component's `serializerMap` if you choose to set it.

## Serialization API

Every `Component` has a `serializer`:

```ts
class Component {
  serializer: Serializer
  ...
}
```

The Serializer exposes the twin methods `serialize` & `deserialize`, used as follows:

```ts
// turn any component into a JSON object
const snapshot = this.serializer.serialize()

// …later – deserialize the JSON object into our component
this.serializer.deserialize(snapshot)
```
Both work recursively.

## `App.autoPersist`

When an `App` is initialized with `autoPersist: true`, domeleon automatically saves after each `update`, and on loading, restores its state via local storage. It's trivial to set up:

```ts
import { App } from 'domeleon'

const app = new App({
  rootComponent : new Master(),
  containerId : 'app',
  autoPersist : true // save & restore automatically
})
```
Internally, this calls `serialize` and `deserialize` on your root component.

## Deserializing classes (and dates)

Declare a `serializerMap` to map plain json objects to classes:

```ts
export class Tree extends Component {
  serializerMap: SerializerMap<Tree> = {
    setting: Setting   // component
    created: Date      // `Date` special case
    trees  : [Tree],   // array of components
    picker : null      // transient component
  }

  setting: Setting  
  created: Date
  trees: Tree[]
  picker: new Picker()
}
```
The types must be classes (`Date` is also handled). This is necessary because Javascript doesn't actually store runtime type information per property; that's a TypeScript only concept that is destroyed in transpilation.

Specifying a `null` value for a field in `serializerMap` prevents serialization. It's typically a useful alternative to prefixing a `Component` property with `_` or `#` for sub-components that you don't want to serialize, but which you still otherwise want to treat as a `Component` (e.g. partaking in updates etc.)

> **Note**: Domeleon's serializer does **not** use decorators for this purpose, as the latest spec is still not natively implemented by browsers as of 2025.

## onDeserialized event

You can handle `onDeserialized` on your `Component` as follows:

```ts
class MyComponent extends Component {
  onDeserialized() {    
    // e.g. perform validation
    this.throwIfInvalid()
  }
}
```

After the component and its children have been deserialized, `onDeserialized` is called, children first, recursively.

>**Note:** `onDeserialized` has early access to `ctx.parent` and `ctx.app` (when available) enabling contextual validation, even if `onAttached` has yet to be called. `onUpdated` will always be called afterwards.

## Handling Sets and Maps

For simplicity, Domeleon only allows nested components to be single instance values or arrays. This makes Domeleon easy to reason about, as well as providing a direct correspondence with our component tree and the JSON format.

We can however, still use Maps. We just need to make sure our map is:

1. Private
2. If public, only exposed via a read-only property, or simply a method.
3. If serialized, expose publcally as an array.

Here's how we could serialize a `collapseMap`:

```ts
export class InspectorComponentTree extends Component {
  _collapseMap = new Map<string, boolean>()

  get collapseState () {    
    return [...this._collapse]
      .map(
        ([id, collapsed]) =>
        ({id, collapsed})
      )
  }

  set collapseState (state: { id: string; collapsed: boolean }[]) {
    this._collapseMap = new Map(
      state.map(
        ({id, collapsed }) =>
         [id, collapsed]
      )
    )
  }
}
```
This means we get the performance benefits of a `Map`, but can fully partake in the domeleon object model.

>**Design Note:** Domeleon may provide a way to reduce this boilerplate in the future; several designs are possible; still figuring out which one is best. For now this is acceptable.

## Related

* [App](./App)
* [Components](./components.md)