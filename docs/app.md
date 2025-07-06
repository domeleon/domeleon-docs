# App

Your Domeleon application always starts by constructing the app. Here's a common setup:

```ts
import { App } from 'domeleon'
import { inspector } from 'domeleon/inspector'
import { themeMgr } from './styles/theme'
import { Master } from './master'

const app = new App({
  rootComponent: new Master(), // create your root component
  containerId: "app", // id of div your root component will live within  
  cssAdapter: themeMgr.unoCssAdapter,
  autoPersist: true,
  plugins: [inspector]
})
```

## `autoPersist`

If true, then your component tree will automatically be serialized to local storage. This can be really nice when developing, as you don't lose your state as you update your source code. However, you may need to add serialization annotations (see [Serialization](./serialization.md) section).

## Related

* [Serialization](./serialization.md)
* [UnoCss](./unocss.md)
* [Pluggable VDOM - React/Vue/Preact](./pluggable-vdom.md)