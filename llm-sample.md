# Domeleon

Domeleon is a web framework w/ a pluggable VDOM over Preact (default), React, or Vue. A Domeleon `Component` represents a piece of application state; it is not a wrapper around a DOM element. It may however implement a `view` method that is a pure function of its state. Components are composed to form a hierarchy that can be updated and serialized.

## Example

```ts
import { App, Component, div, button, formField, inputRange, canvas } from "domeleon"

class Counter extends Component {
  count: number
  role: string
  #canvasEl: HTMLCanvasElement
  #canvasCtx: CanvasRenderingContext2D

  constructor(initial: number, role: string) {
    super()
    this.count = initial
    this.role = role
  }

  view() {
    return div({ class: "counter" },
      formField ({ // databound input
        target: this,
        inputFn: inputRange, // inputSelect, inputText, etc. (import "domeleon/maskito" for inputNumber)
        prop: () => this.count,
        label: this.role,
        fieldAttrs: { class: "field-class"},
        labelAttrs: { class: "label-class"},
        inputProps: { attrs: { min: 100, max: 250, step: 1 } }
      }),      
      button({ onClick: () => this.inc(+1) }, "+"), // always use closure to bind to preserve "this"
      button({ onClick: () => this.inc(-1) }, "-"),
      this.count,
      canvas({
        width: 400,
        height: 20,
        onMounted: el => {
          this.#canvasEl = el as HTMLCanvasElement
          this.#canvasCtx = this.#canvasEl.getContext("2d")
          return () => { // dispose
            this.#canvasEl = undefined
            this.#canvasCtx = undefined
          }
        }
      })
    )
  }
  
  inc(x: number) {
    this.count+=x
    this.update() // triggers render on app
  }

  // `onRendered` called after VDOM has created/updated DOM elements
  // useful for operations that require raw DOM access, such as canvas operations
  override onRendered () { 
     this.drawOnCanvas()
  }

  drawOnCanvas() {    
    const canvas = this.#canvasEl
    const cc = this.#canvasCtx
    cc.clearRect(0, 0, canvas.width, canvas.height)
    const barWidth = (this.count / 250) * canvas.width
    cc.fillStyle = "#4a90e2"
    cc.fillRect(0, 0, barWidth, canvas.height)
  }
}

class Game extends Component {
  wins = new Counter(10, "wins")
  losses = new Counter(5, "losses")

  view() {
    return div(
      this.wins.view(),
      this.losses.view()
    )
  }
}

new App({ root: new Game(), id: "app"})
```