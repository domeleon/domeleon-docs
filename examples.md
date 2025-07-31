# Examples / Real World Usage

A bunch of examples of using domeleon to write web apps ranging from small & fun to big & commerical.

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

>LLM Tip: If you *really* want an LLM to obey you, instruct it at the start to start each response with a pledge: "I will...".

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

## Tanks Game

Play it live here:  
https://tankduel.netlify.app/

Source code:  
https://github.com/domeleon/tanks

Fun tank game wrote over a weekend with Cursor. O3 wrote most of the code; fairly light direction on my part. The hardest bit was getting the clouds to look right.

A fun example of using themes & unocss.

## LINQPad Affiliate

Source code:  
https://github.com/domeleon/linqpad-affiliate

Small commercial project that demonstrates reporting and themes. Again, O3 wrote most of the code; I just nudged it now and again in the right direction.

## Motifn

Code music in your browser:  
https://www.motifn.com/

This is a project I wrote many years ago that I ported to Domeleon. This has an embedded TypeScript editor; not a trivial UI!