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

When building a Domeleon application, run the script (TBD), to create a folder `LLM/domeleon` that your LLM can refer to. This folder will contain:

* The above documentation
* The .d.ts files, that include the API signatures and jsdocs
* One or two sample applications (TBD)
* The domeleon source code

That's the advantage of a small, modular framework; an LLM can easily digest and reason about it. Even in its entirety, all the aforementioned content about Domeleon easily fits in an LLM's context window.
