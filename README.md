# Domeleon Docs

Build web UIs with classes encapsulating your domain model and a pluggable Preact/React/Vue VDOM.

There's plenty of domeleon examples on Typebulb, a Typescript Scratchpad which was itself written in domeleon:  
https://typebulb.com/

Some extra [domeleon apps here](./examples.md).

**Domeleon is optimized for LLM usage; described further below.**

Here's the conceptual documentation:

## Topics

* [Components](./docs/components.md)
* [Html](./docs/html.md)
* [Forms](./docs/forms.md)
* [Validation](./docs/validation.md)
* [Unocss](./docs/unocss.md)
* [Routing](./docs/routing.md)
* [Serialization](./docs/serialization.md)
* [App](./docs/app.md)
* [Pluggable VDOM for React/Vue/Preact](./docs/pluggable-vdom.md)
* [Inspector](./docs/inspector.md)
* [Maskito](./docs/maskito.md)
* [Component Communication](./docs/communication.md)

## LLM Usage

Domeleon is a concise, modular framework, making it ideal for an LLM to digest and reason about. Even the entire code base with documentation easily fits inside its context window.

When starting a Domeleon application, point your LLM inside Cursor or WindSurf to the [LLM Readme](./llm-readme.md) and tell it to follow the instructions. This will create a folder `LLM/domeleon` that your LLM can refer to, containing:

* The above documentation
* The API signatures w/ jsdocs
* The domeleon source code

Then, when you ask your LLM to create a Domeleon app, it can do so far more reliably.