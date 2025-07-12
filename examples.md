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

Was vibe coded using an LLM using no TypeScript, no build process, only CDN imports, all in one html page. I'm being rather liberal with the term "vibe" here; some parts would have been faster to write from scratch, such as the code that splits the computation into chunks so that the worker incrementally updates the UI. LLMs haven't stolen my income quite yet ;)

It's also on codepen:
https://codepen.io/antypica/full/wBvoWdL

## Game Of Life

This is a nice little test app. I wrote this with an LLM twice; once with ChatGPT and once with Replit. The LLM will ocassionally contradict the code samples & documentation you give it, so be firm and tell it that no, it needs to trust the docs & samples.

### ChatGPT

Use the raw HTML of the following page as a starting point for a Domeleon app; just paste it into ChatGPT:  
[Game of Life by ChatGPT](./gameOfLife.html)

This was built using o4-mini-high. If you manually feed the LLM enough code and docs, it'll 1 shot.

### CodePen
Here's the ChatGPT code above dumped on codepen:  
https://codepen.io/domeleon/pen/azvzJPP

### Replit
Here's Replit's crack at the Game of Life:  
https://replit.com/@benalbahari/GameOfLife

This one uses domeleon/unocss for styling. I pasted in the domeleon/unocss document to guide it.