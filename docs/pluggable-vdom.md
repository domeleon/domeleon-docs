# Pluggable VDOM - React/Vue/Preact

Domeleon's VDOM is pluggable. The specific VDOM you choose should mostly be an implementation detail; you can write large applications with Domeleon, swap out the VDOM, and not even notice. All 3 VDOMs perform well and are very robust.

By default Domeleon uses Preact, as it's tiny, making it a perfect default. However, you can also use Domeleon with React or Vue, which is useful if you really need to leverage a particular component only available in that framework.

## `with*` Attributes

We can integrate with renderer-specific hooks and components from Preact, React, and Vue, with `with*` attributes.

Here we animate the shuffle using `useAutoAnimate` with preact:

```ts
import { div, button } from 'domeleon'
import { useAutoAnimate } from '@formkit/auto-animate/preact'

export class Shuffler extends SampleComponent {
  items = [1,2,3,4,5]

  view() {    
    return div(
      div({ withPreact: { hook: useAutoAnimate } }, 
        this.items.map(i =>
          div({ key: i }, i)
        )
      ),
      button({ onClick: () => this.shuffle() }, "Shuffle")
    )
  }

  shuffle() {
    this.items.sort(() => Math.random() - 0.5)
    this.update()
  }
}
```
`useAutoAnimate` is a wonderful component, and provides hooks for all 3 frameworks. However, let's swap out our renderer with `Vue` and use Vue's `TransitionGroup` component as follows:

```ts
      div({
        withVue: {
          component: TransitionGroup,
          componentProps: { tag: 'div', name: transitionName }
        }
      },
        this.items.map(i =>
          div({ key: i }, i)
        )
      ),
```
You can even include *both* `withPreact` and `withVue`. If the renderer doesn't match, it's simply ignored.

## Choosing a Renderer

You choose a renderer when you launch your app.

### Preact / Deafult

Here's the default, that implicitly uses Preact:

```ts
import { app } from 'domeleon'
import { Index } from './index'
...

app({root: new Index(), id: "app"})
```
### Vue

Here's using Vue:

```ts
import { VueRenderer } from 'domeleon/vue'
import { app } from 'domeleon'
import { Index } from './index'

app({
  root: new Index(),
  id: "app",
  renderer: new VueRenderer()
})
```

### React

Here's using React:

```ts
import { ReactRenderer } from 'domeleon/react'
import * as React from 'react'
import * as ReactDOMClient from 'react-dom/client'
import { app } from 'domeleon'
import { Index } from './index'
...

app({
  root: new Index(),
  id: "app", // or "root" if you prefer that for name of the id of the element to mount on
  renderer: new ReactRenderer({ ReactLib: React as any, ReactDOMClientLib: ReactDOMClient })
})
```
React requires separated React and Client libaries that you import yourself, for configurability and versioning robustness.

## Be Agnostic When you Can

Many of the best 3rd party libraries are vendor neutral, and you can use them simply via the `onMounted` hook, that works across all renderers.

## Related Topics

* [HTML](./html.md)
* [App](./app.md)