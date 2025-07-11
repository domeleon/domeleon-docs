# Examples / Real World Usage

## Motfin
Code Music in the browser:  
https://www.motifn.com/

A good example of building a non trivial app.

## Infinite Mandelbrot Explorer
https://antypica.com/mandelbrot.html

A Mandelbrot Explorer on steroids:

* 1e300 zoom depth, perturbation algo
* Animated zoom & progressive rendering
* Full screen
* Mobile friendly
* Snapshot/save current image
* Built with WebGL2, WebAssembly & Web Workers

Zoom example:  
https://antypica.com/mandelbrot.html?z=5.02e24&iterations=5000&x=-1.256822095990453833949454069&y=0.379714613427455344693340029

Was vibe coded using an LLM using no TypeScript, no build process, only CDN imports, all in one html page.

It's also on codepen:
https://codepen.io/antypica/full/wBvoWdL

## Code Pen: Counter Sample
In case you need a quick kickstart:  
https://codepen.io/domeleon/pen/azvbyVP

But really, just use Cursor or Windsurf, and get your LLM to read the [LLM Readme](./llm-readme.md). Ask it to build the Game of Life; often succeeds cold in 1 try.

## Replit: Game of Life Sample
Use this as a starting point:  
https://replit.com/@benalbahari/GameOfLife

To make that work, I just manually fed it the Code Pen Counter sample & the unocss document. It made more mistakes than Cursor's auto model (e.g. for some reason it erroneous added square brackets around the uno css variables, contradicting the code in the docs I gave it), but with a little intervention, was still a decent result.