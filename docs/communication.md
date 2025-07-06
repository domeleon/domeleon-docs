# Component Communication

## Child-To-Parent Communication

Generally, a child doesn't *need* to talk to its parents; it looks after itself, and delegates to its own children. However, sometimes the best way to manage complexity is to break this generalisation. There are 3 main patterns for doing so:

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

```ts
  // define common interface both ISetting and parent can use
  interface ISetting { tick: number }

  // Parent implements ISetting
  class Parent extends Component implements ISetting {
     // implement tick
  }
    
  // Child uses ISetting
  class Child extends Component {
    getSettings() { return this.parent as ISetting }
    foo() {
      const tick = this.getSettings().tick
    ...
    }
  }
```

### Update Path

In this child-to-parent communication pattern, the parent overrides `onUpdated (event: UpdateEvent)` to react to *any* update originating from its children (or itself).
  ```ts
  // Parent
  onUpdated(event: UpdateEvent) {
     ...
  } 
  ```
## Component to Non-Component Communication

Within the component layer, *maintain a single source of truth*, i.e. avoid duplicating state across components. But when crossing boundaries, consider *synchronizing* between your component and the other object's state.

### Synchronization Examples

`Component` + DOM Element: Use `inputXxx` databinding functions to automatically synchronize between your component state and DOM state.

`Component` + `router`: Use the `onNavigate`/`onNavigated` events on `Component` to synchronize between your component and the browser's location captured in your component `router`'s `selectedSegment`.

>**💡Guideline:** if the state in question should be serialized with your component, it belongs on your component, if it merely emphemerally exists elsewhere at the same time.

## Related Topics

* [Components](./components.md)