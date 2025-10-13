# Routing

Domeleon provides a composable router, where each `Component` that's routed has its own `router` property, and `routeSegment`:

``` ts
export class Help extends Component
{
  router: Router = new Router(this)  
  routeSegment = "help"
}
```


The Help component will now match the `help` route.

> **💡Route Tree Structure**  
> The route structure mirrors your component tree structure (but skips *transparent* routes).

> **💡Inspect Routes**  
> Domeleon's inspector especially highlights your route structure, and informs you of router update events.

## Navigation

Use the router's `navigate` method with *relative* paths. The below assumes `this` is the `Help` component:

| Code | From | To |
|------|-------------|------------|
| `this.router.navigate('')` | `/help` | `/help` |
| `this.router.navigate('banana')` | `/help` | `/help/banana` |
| `this.router.root.navigate('help/banana')` | `/` | `/help/banana` |
| `this.router.parent.navigate('examples')` | `/help` | `/examples` |

## Root router

Each router on your component tree also forms its own router tree. The root of that tree must define a *transparent route* (explained further below), meaning it has an empty `routeSegment`:

```ts
class Index {
  router: Router = new Router(this)
  routeSegment = ""
}
```

>Note that you can define `basePath`, explained further below.

## Intercepting Navigation

Implement your `Component`'s' `onNavigate` for:

*   **Validation/Prevention:** (return `false` to prevent navigation)
*   **Data Fetching:** (await retrieving child components)
*   **Redirection:** (cancel, then navigate elsewhere)

Implement as follows:

```ts
async onNavigate (relativeRoute: Route) {
  if (child.firstSegment == ...) {
    // return false to cancel navigation
  }
  const child = await ... // fetch child
  this.serializer.deserialize ({ child: child})
}
```
You may also implement the synchronous `onNavigated` to react *after* a component becomes active in the route.

### Route Class
The `Route` object provides a safe, structured view of a route. Access its `segments` or convenient `firstSegment`, that's simply `segments[0]`.

> Never engage in error-prone string manipulation of forward slashes to deal with paths! Use the `Route` class instead.

`firstSegment` is the most common property to use in `onNavigate` as it represents the the immediate relative path, that is intended to match a child component's `routeSegment`.

## Cancelling/Guarding Naivation

Return `false` to cancel/gaurd a navigation; otherwise you don't need to return anything.

## Preparing Child Routes

A common pattern is to fetch data on `onNavigate`, so that a child can be navigated to. Domeleon strictly requires an *exact match* based on the `routeSegment`, so dynamic routes require this pattern. Think of it as Just-In-Time routing.

> There's no wildcard matching in Domeleon routes! Why? There must *always* be a `component` that represents the exact *state* of a given route. You can't navigate to `"canteloupe"` unless a component exists with the `routeSegment` with the value `"canteloupe"`.

## Navigation Links

The router has a `link` method that's very useful for creating `<a href="...">` virtual DOM nodes. You simply provide a relative route (as a `string`|`string[]`|`Route`), and `HValues`. For example:

```ts
// Example: Creating a link to the 'banana' tab
this.router.link('banana', 'Go to Banana Tab')
// Renders an <a> tag with appropriate href and an `onClick` handler.
```
## Transparent Routes

Transparent routes are incredibly useful as they relax the strict 1-1 relationship between the component tree and route tree. They allow a component purely to pass through the route. For example, `Sidebar` has a transparent route:

```ts
class Sidebar extends Component {  
  router: Router = new Router(this)
  routeSegment = ""

  help = new Help()
}
```
Transparent routers still receive `onNavigate` and `onNavigated` events. They just don't contribute to the `path`; think of it like parents who leave their kids with their grandparents.

## RouteService

You can explicitly initialize the `RouteService` in the `App` construtor to set of a `basePath`. This will ensure your entire router setup works relatively to that path.

```ts
new App ({
   ...
   routeService: new RouteService { basePath: "admin"}
})
```
By default, a `RouteService` is created for you with an empty `basePath`.

# Related

* [Components](./components.md)
* [Inspector](./inspector.md)