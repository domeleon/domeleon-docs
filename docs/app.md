# App

Your Domeleon application always starts by constructing an `App` instnace.

Here's a minimal setup:

```ts
import { App } from 'domeleon'

new App({ root: new Root(), id: "app"}) // root component + id of element to mount
```
Here's a more advanced setup:

```ts
import { App } from 'domeleon'
import { inspector } from 'domeleon/inspector'
import { themeMgr } from './styles/theme'
import { Master } from './master'
import { VueRenderer } from 'domeleon/vue'

new App({
  root: new Root(), 
  id: "app",
  cssAdapter: themeMgr.unoCssAdapter, // pass .class values through per render
  renderer: new VueRenderer(), // altenatively a ReactRenderer
  autoPersist: true, // auto serialize component tree to local storage
  routeService: new RouteService { basePath: "admin"} // only needed for a custom base path
  plugins: [inspector] // inspect component tree and events
})
```

## `autoPersist`

If true, then your component tree will automatically be serialized to local storage. This can be really nice when developing, as you don't lose your state as you update your source code. However, you may need to add serialization annotations (see [Serialization](./serialization.md) section).

## Related

* [Serialization](./serialization.md)
* [UnoCss](./unocss.md)* 
* [Pluggable VDOM for React/Vue/Preact](./pluggable-vdom.md)
* [Routing](./docs/routing.md)
* [Inspector](./docs/inspector.md)