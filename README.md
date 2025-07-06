# Domeleon Docs

Build web UIs with classes encapsulating your domain model and a pluggable Preact/React/Vue VDOM.

**Domeleon is optimized for LLM usage; described further below.**

Here's a [Quick Start / Overview](https://github.com/domeleon/domeleon).

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

When starting a Domeleon application, point your LLM inside Cursor or WindSurf to the [Domeleon LLM Prep Guide](./domeleon-llm-prep-guide.md) and tell it to follow the instructions. This will create a folder `LLM/domeleon` that your LLM can refer to, containing:

* The above documentation
* The .d.ts files, that include the API signatures and jsdocs
* The domeleon source code

Then, when you ask your LLM to create a Domeleon app, it can do so from scratch with an extremely low error rate.