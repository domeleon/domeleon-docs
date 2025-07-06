# Unocss

You can use whatever CSS framework you want with Domeleon. However, Domeleon has a specific add-on for `unocss` that provides a theme manager that allows you to express type-safe, terse, css code.

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
Now you can use, with guaranteed type safety, as follows:

```ts
  view() {
    div ({class: tabStyles.active }, ...)
  }
```
Benefits of this approach:

* Effortlessly find or rename all usages of any specifc css variable, e.g. `bgSecondary`, throughout your entire app
  * Confidently delete unused styles
* Class names are semantically named and scoped, such as `app-tab-active`:
  * Zero collisions, short, & human radable
  * The prefix, such as `tab` above, is optional. So `themeMgr.styles(theme => { ...})` is also ok
* Frictionless switching between custom themes, e.g. light vs dark
* Choose whatever organisation keeps your css code maintainable:
  1. Colocate styles with components
  2. Keep styles in their own file

>Note: `themeMgr.styles` internally leverages unocss shortcuts. However, you can also just directly use utility styles for `class` properties if you wish.

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

Now let's suppose you want a dark theme:

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

Where you add both themes in your theme manager:

```ts
  themes: {
    light: new LightTheme(),
    dark: new DarkTheme()
  }
```
You can then set the `themeName` of your themeManager whenever you want to change themes. Or you can configure it based on your users preference as follows:

```ts
const isDark = window.matchMedia('(prefers-color-scheme: dark)').matches  

export const themeMgr = new UnoThemeManager({
  ...  
  initialTheme: isDark ? 'dark' : 'light',
})
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

## Unocss Imports

You'll need to pick an unocss preset, then use the UnoThemeManager and ThemeProxy for domeleon, as follows:

```ts
import presetWind3 from '@unocss/preset-wind3'
import { UnoThemeManager, type ThemeProxy } from 'domeleon/unocss'
```

## Dynamic Styling

Domeleon's `unocss` add-on computes **all** styles ar runtime, not build time. Why?

* The underlying `unocss` library is *incredibly* fast, even for the first render.
* Even the presets are fairly small in kb.
* No build step = ultra simple deployment.
* Domeleon has deep integration, bypassing any FOUC issues.

### Sticky Situation - Advanced Usage Only

One issue with dynamically generated styles, is occassionally you can build up too many styles in the head of your document. However, there's an escape hatch.

The `UnoThemeManager` class exposes its `UnoCssAdapter`:

* By default its `stickyMode` is `always`, so classes always persist across renders.
* If you set `stickyMode` to `explicit` then only classes passed through the `stickyClass` function perist across renders. Import and use `stickyClass` for special cases like animation, where you need to guarantee classes "stick" around.

You can also call the `UnoCssAdapter`'s `clearStickyClasses` to force clear the sticky styles.

## Related

* [HTML](./html.md)