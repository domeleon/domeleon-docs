# Component Communication

## Child-To-Parent Communication

Generally, a child doesn't *need* to talk to its parents; it looks after itself, and delegates to its own children. However, sometimes the best way to manage complexity is to break this generalisation. There are 4 main patterns for doing so:

### Callbacks

In this child-to-parent communication pattern, the parent passes functions down to child views/components:

```ts
  // Parent
  view() { return this.child.view({ onSave: () => this.handleSave() }) }
  handleSave() { this.update(...) }

  // Child
  view(props: { onSave?: () => void }) { ... }
```

### Parent Interface

In this child-to-parent communication pattern, the child accesses its parent via `this.ctx.parent`. The child then casts the parent to an interface that limits scope and prevents circular type references. Now the child can read state or call methods.

In this example, we want the `Animal` component to get its family's `name`, so we allow this via an `IFamily` interface:

```ts
  interface IFamily { name: string }

  class Family extends Component implements IFamily {
    name: string
    animals: Animal[]
  }
    
  class Animal extends Component {
    get family() { return this.ctx.parent as IFamily }  
    
    foo() {
      const name = this.family.name
    }
  }
```

### Root Interface

Building on the previous idea, sometimes it's useful for the `root` component to expose an interface, that any deeply nested child component can reference.

Here our root component implements `IZoo`:

```ts
  interface IZoo {
     families: IFamily[]
  }

  class Zoo extends Component implements IZoo {
     families: Family[]
  }

  class Family {...}
    
  class Animal extends Component {
    get zoo () { return this.root as IZoo }
    goo() {
      const pride = this.zoo.families.find(f => f.name == "Lion")      
    }
  }
```
Key things to remember:

* Keep your interfaces limited. Don't expose more than you need to; the less you expose the less you need to reason about.
* Avoid children accessing parents, but only to a point: if you're tying yourself in knots trying to be "pure", you're merely trading one type of complexity for another.

### Update Path

In this child-to-parent communication pattern, the parent overrides `onUpdated (event: UpdateEvent)` to react to *any* update originating from its children (or itself).
  ```ts
  // Parent
  onUpdated(event: UpdateEvent) {
     ...
  } 
  ```
This will also catch any input, validator, router & serializer events.

>Tip: To know what update events pass through `onUpdated`, use the Inspector.

## Component to Non-Component Communication

Within the component layer, *maintain a single source of truth*, i.e. avoid duplicating state across components. But when crossing boundaries, consider *synchronizing* between your component and the other object's state.

### Synchronization Examples

`Component` + DOM Element: Use `inputXxx` databinding functions to automatically synchronize between your component state and DOM state.

`Component` + `router`: Use the `onNavigate`/`onNavigated` events on `Component` to synchronize between your component and the browser's location captured in your component `router`'s `selectedSegment`.

>**💡Guideline:** if the state in question should be serialized with your component, it belongs on your component, if it merely emphemerally exists elsewhere at the same time.

## Related Topics

* [Components](./components.md)
* [Inspector](./inspector.md)