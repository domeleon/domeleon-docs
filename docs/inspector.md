# Inspector

The `inspector` is a tool that lets you see your component tree and monitor update events.

Set up the inspector as follows:

```ts
import { app } from 'domeleon'
import { inspector } from 'domeleon/inspector'

app({
  ...
  plugins: [inspector]
})
```
Components with a `router` property have their routes displayed, making it a great way to get an overview of your routing setup.

The types of update events are:

* router
* validator
* input
* serializer
* custom

`inspector` is itself an application written with Domeleon, and uses the `@unocss/preset-wind3` preset as a peer dependency.

# Related

* [App](./app.md)