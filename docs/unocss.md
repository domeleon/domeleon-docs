# Unocss & Theming

You can use whatever CSS framework you want with Domeleon. However, Domeleon has a specific add-on for `unocss` that provides a theme manager that allows you to express type-safe, terse, css code.

## Canonical Usage

For example, here's styling for a tab component:

```ts
const tabStyles = themeMgr.styles("tab", theme => {
  const { textPrimary, bgSecondary, bgAccent } = theme.colors

  return {
    active: `text-${textPrimary} border-b-${bgAccent} font-bold`,
    inactive: `text-${bgSecondary} border-b-transparent font-normal`
  }
})
```
Then use styles with guaranteed type safety as follows:

```ts
  view() {
    div ({class: tabStyles.active }, ...)
  }
```
## Benefits

For most apps, using domeleon's unocss theme manager will save you time:

* Effortlessly find or rename all usages of any specifc css variable, e.g. `bgSecondary`, throughout your entire app
  * Confidently delete unused styles
* Class names are semantically named and scoped, such as `app-tab-active`:
  * Zero collisions, short, & human radable
  * The prefix, such as `tab` above, is optional. So `themeMgr.styles(theme => { ...})` is also ok
* Frictionless switching between custom themes, e.g. light vs dark
* Choose whatever organisation keeps your css code maintainable:
  1. Colocate styles with components
  2. Keep styles in their own file

## Setting Up a Theme

Here is how you set up a theme:

```ts
const lightTheme = {
  colors: {
    textPrimary: 'rgb(0, 187, 0)',
    textSecondary: 'rgb(200, 200, 200)',
    textDisabled: 'rgb(82, 82, 82)',
    pianoRoll: {
      barLine: "rgb(136,136,136)",
      barLabel: "rgb(255,255,255)"
    }
  }
}

export const themeMgr = new UnoThemeManager({
  id: 'app',  
  themes: { light: new LightTheme() },
  unoCssConfig: { presets: [presetWind3()] }
})
```

## Multiple Themes

It's easy to add multiple themes. Here's adding a dark theme:

```ts
const darkTheme = {
  colors = {
    textPrimary: 'rgb(0, 187, 0)',
    textSecondary: 'rgb(200, 200, 200)',
    textDisabled: 'rgb(82, 82, 82)',
    pianoRoll: {
      barLine: "rgb(136,136,136)",
      barLabel: "rgb(255,255,255)"
    }
  }
}
```

Where we now add both the `light` and `dark` themes to our theme manager:

```ts
  themes: {
    light: new LightTheme(),
    dark: new DarkTheme()
  }
```
## Dynamically Changing Themes

You simply set the `themeName` of your theme manager whenever you want to change themes, at runtime. Or you can configure it based on your users preference as follows:

```ts
const isDark = window.matchMedia('(prefers-color-scheme: dark)').matches  

export const themeMgr = new UnoThemeManager({
  ...  
  initialTheme: isDark ? 'dark' : 'light',
})
```

## Imports and `App` Setup

You'll need to pick an unocss preset, then use the `UnoThemeManager` and `ThemeProxy` for domeleon, as follows:

```ts
import presetWind3 from '@unocss/preset-wind3'
import { UnoThemeManager, type ThemeProxy } from 'domeleon/unocss'
```
Make sure to specify your theme manager's `cssAdapter` in your app:

```ts
new App({
  ...
  cssAdapter: themeMgr.unoCssAdapter  
})
```
This guarantees any fresh `class` properties on your `VElement` (`div`, `span` etc.) are sent through to the adapter just before each VDOM patch, with no FOUC (Flash of Unstyled Content).

## Bypassing UnoCss Shortcuts

`themeMgr.styles` internally leverages unocss shortcuts, which means you get clean, short, semantically named class properties in your css. You can however, directly use utility styles on `class` properties if you wish. You can even reference your theme manager's styles and it will work fine. For example:

```ts
  div({ class: `text-${bgSecondary} border-b-transparent font-normal`})
```

## Global Styles

### globalUnoCss

Sometimes you need to specify styles *globally*, such as wanting your links to look a particular way across your entire application. You define them with a `globalUnoCss` definition:

```ts
const globalUnoCss = (theme: ThemeProxy<MotifnTheme>) => {
  const { textPrimary, primaryHover, backgroundPrimary, textSecondary, } = theme.colors
  return {
    'a': `text-${textPrimary} no-underline`,
    'a:hover': `text-${primaryHover}`,
    'h1': `m-0 text-${textPrimary}`,
    'body': `m-0 bg-${backgroundPrimary} text-${textSecondary} font-sans antialiased`
  }
}

export const themeMgr = new UnoThemeManager({
  ...
  globalUnoCss
})
```

Be judicious with global styles; in most cases you don't need them.

### globalRawCss

Sometimes you'll want to define raw css, not unocss utility strings. This is useful in certain cases, such as for keyframe animations. You define them with a `globalRawCss` definition:

```ts
const globalRawCss = (theme: ThemeProxy<MotifnTheme>) => {
  const { highlightAnimateStart, highlight } = theme.colors
  return `
    @keyframes nowStroke {
      0%   { stroke: ${highlightAnimateStart.css}; }
      100% { stroke: ${highlight.css}; }
    }
    @keyframes nowFill {
      0%   { fill: ${highlightAnimateStart.css}; }
      100% { fill: ${highlight.css}; }
    }
    .now { animation: nowStroke 0.5s linear; }
    .now-note-label { animation: nowFill 0.5s linear; }
  `
}

export const themeMgr = new UnoThemeManager({
  ...
  globalRawCss
})
```
Once these global styles are defined, you can refer to them into your ordinary unocss styles.

## `CssVar`—Advanced Usage

You almost always use theme properties such as `textPrimary` within a string literal. This means its `toString` method is called, that simply returns its `name`, for `unocss` usage.

Its type is `CssVar` which has other properties that are occasionally useful if you need to work at lower layer, as we did when using `globalRawCss`. For example:

```
name:        textPrimary
cssVarName:  --app-colors-text-primary
css:         rgb(var(--app-colors-text-primary))
rawValue:    rgb(0, 187, 0)
```
>**Deep Dive**: The proxy generated by the theme manager takes your raw string values and returns `CssVar` values.

## Transparency / Alpha / Opacity

Always separate the alpha values from your colors. For example:

```ts
const lightTheme = {
  colors: {
    textPrimary: 'rgb(0, 187, 0)'    
  },
  // optionally define one or more groups holding alpha values
  alphas: {
    panel: '0.5' // note: always use strings for theme variables
  }
}
```
Now, to make `textPrimary` 50% transparent, you have two options:

 * Option 1: Simply inline `50` with unocss; this is more common.
 * Option 2: Pass a theme variable to the `alpha` method of a color theme variable.

Both are shown below, yielding the same result:
```ts
const styles = themeMgr.styles("test", theme => {
  const { textPrimary } = theme.colors
  const { panel } = theme.alphas

  return {
    option1: `text-${textPrimary}}/50`
    option2: `text-${textPrimary.alpha(panel)}`...
  }
})
```
Using an alpha theme variable is useful if your transparency values diverges per theme.

## Dynamic Styling

Domeleon's `unocss` add-on computes **all** styles ar runtime, not build time. Why?

* The underlying `unocss` library is *incredibly* fast, even for the first render.
* Even the presets are fairly small in kb.
* No build step = ultra simple deployment.
* Domeleon has deep integration, bypassing any FOUC issues.

### Sticky Situation—Advanced Usage

One issue with dynamically generated styles, is occassionally you can build up too many styles in the head of your document. However, there's an escape hatch.

The `UnoThemeManager` class exposes its `UnoCssAdapter`:

* By default its `stickyMode` is `always`, so classes always persist across renders.
* If you set `stickyMode` to `explicit` then only classes passed through the `stickyClass` function perist across renders. Import and use `stickyClass` for special cases like animation, where you need to guarantee classes "stick" around.

You can also call the `UnoCssAdapter`'s `clearStickyClasses` to force clear the sticky styles.

## Related

* [HTML](./html.md)