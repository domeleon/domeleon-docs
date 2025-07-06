# Inspector

The `inspector` is a tool that lets you see your component tree, route structure, as well as monitor all your component update events, specifically:

* router
* validator
* input
* serializer
* custom events

Set up the inspector as follows:

```ts
import { App } from 'domeleon'
import { inspector } from 'domeleon/inspector'

const app = new App({
  rootComponent: new Master(),
  containerId: containerId,  
  plugins: [inspector]
})
```

`inspector` is itself an application written with Domeleon, and uses the `@unocss/preset-wind3` preset as a peer dependency.

# Related

* [App](./app.md)