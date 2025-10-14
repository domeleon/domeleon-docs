# Forms

Domeleon lets you choose 4 increasing layers of abstraction when building forms, to suit your needs:

| Layer | Description | API |
|-------|-------------|----------|
| Raw Input | Thin wrappers around the `h` function returning a `VElement` | `input`, `select`
| `inputXXX` | Functions that databind to your components | `inputText`, `inputTextArea`, `inputSelect`, `inputRange`, `inputNumber` (via maskito), `inputMask` (via maskito), `inputCheckbox`, `inputRadioGroup`, `inputCheckboxGroup` |
| `formField` | Takes an `inputXXX` function and automatically outputs standardized vdom elements with: labelling/description, validation, aria attributes, id generation, structural compatibility with major css frameworks | `formField` |
| `formField` wrapper | Your own helpers that wrap `formField` for consistent styling throughout your application. | `myFormField`
   
> 💡**Note**: By design, Domeleon **never** outputs css classes; this is entirely handled in your application layer. For example, you set the class on the `validationAttrs` property of `formField`; domeleon is 100% not prescriptive here.

## Raw Input Layer (lowest `h` layer)

This is simply directly using the standard auto-generated `h` wrappers based on the HTML spec:
```ts
input ({id: "foo", value: "name": onInput: () => { ... } })
```
## InputXXX Layer (databinding layer)

The following functions are abstraction of the raw input layer, to handle databinding for you:

* `inputText`
* `inputTextArea`
* `inputSelect`  
* `inputRange` 
* `inputNumber` // maskito
* `inputMask` // maskito
* `inputCheckbox`
* `inputRadioGroup`
* `inputCheckboxGroup`

You can use these `inputXxx` functions directly, or you can merely pass the function into `formField` as the `inputFn`. Here's using one directly:

```ts
export class Form extends Component {
  country = `UA`

  view () {
    return inputSelect({
      target: this,
      prop: () => this.country,
      options: [
        {value: "UA", label: "Ukraine"},
        {value: "GE", label: "Georgia"}
      ]  
    })
  }
}
```

Every `inputXxx` function takes a `target` and `prop` value, to perform the databinding. Some `inputXxx` functions take additional arguments, such as `inputSelect` that takes an array of `options`.

## formField

Here's how we could use an input function with `formField`. Note that in a typical app we'd probably write a reusable formField, to minimise boilerplate, as we'll see later.

```ts
import { Component, div } from 'domeleon';

export class Form extends Component {
  country = `UA`
  view () {
    return formField({
      target: this
      prop: () => this.country,
      inputFn: inputSelect,
      inputProps: {
        options: [
          {value: "UA", label: "Ukraine"},
          {value: "GE", label: "Georgia"}
        ] 
      }
    })
  }
}
```
The `inputProps` simply passes through the properties to `inputFn`.

So why use `formField` rather than just directly use an `inputXxx` function?

`formField` will **automatically**:

* include a human readable label and/or description, either driven by the component's field name by default, or by the `getLabel` method if implemented on the component
* asssign unique `id`s for the label and descriptions to work properly (using `component.ctx.qualify(prop)`).
* output aria attributes
* if validation is enabled, output validation errors
* include a ubiqituous element structure compatible with all major css frameworks

Swapping from bootstrap to unocss? Your form fields stay the same: just update the css classes you feed them. `formField` helps you deeply separate the concerns of your form's data vs. presentation.

## Writing a `formField` wrapper

It's common to write a wrapper for `formField`, for consistent styling across your app. Your usage can look like this:

```typescript
  appFormField ({
    target: this,
    prop: () => this.country
  )
```

Where you've defined a reusuable `appFormField`. Here is where you can set every facet of the `formField`, to precisely control the styling.

```typescript
type SupportedInputFn = typeof inputText | typeof inputNumber | typeof inputMask

const appFormField = (props: FormFieldInputProps<SupportedInputFn>) => 
  return formField({
    ...props,
    // common customization
    fieldAttrs: { class: styles.field }, // div wrapping entire field
    descriptionAttrs: { class: styles.description },
    validationAttrs: { class: styles.error } // e.g. red    
  })
```
`formField` always provides a unified structure, regardless of whether we're using text inputs, select inputs, or radio buttons:

* `target` // the component to databind to
* `prop` // the property on the component to databind to, expressed in a type safe manner
* `inputFn` // the name of the input function, e.g. `inputText` or `inputSelect`
* `inputProps` // the props the `inputFn` expects.

## Labels and Descriptions

### Default Labels

By default, `formField` automatically generates a label name by converting `yourProperty` to `Your Property` using the `humanizeIdentifer` function. It translates like this:

* `yourPropertyName` -> `Your Property Name`

### `getLabels`

If you need more control, or a more metadata driven approach, you can implement the `getLabels` function. This will automatically be picked up by `formField` when setting the `label` and `description` properties. Use as follows:

```ts
export class MySample extends Component {
  username: string
  bonus: number

  getLabels() {
    return {
      username: { label: "What's your name?", description: "Nick names are ok!" },
      bonus: "Annual Bonus"  
    }
  }
```
You can specify either just a `string` for label, or an object, if you wish to provide both a `label` and a `description`.

The `description` text by default appears below your input, and is swapped out by a validation message, if a validation error occurs.

### Explicitly Setting `label` and `description`

For maximum control, explicitly set the `label` and `description` values directly when calling `formField.` Here you can set arbtirary HTML rather than just strings. For example:

```ts
    return formField({ 
      target: this,
      prop: () => this.subscribeToNewsletter, 
      inputFn: inputCheckbox,
      label: "Subscribe To Newsletter"
      description: span({/* styles here */}, "Exclusive content!")
    })
```

## Related Topics

* [Validation](./validation.md)
* [Maskito](./forms.md)