# Validation

Domeleon can integrate with validation libraries like `zod` or `class-validator`. The dedicated `domeleon/zod` package provides out of the box support for `zod`. `zod` has a very nice chained model for incrementally refining permissable values.

Suppose we define this `zod` schema:

```ts
const schema = z.object({
  username : z.string().min(3).endsWith("x").max(10).regex(/^[a-z]+$/),
  rating   : z.number().min(0).max(10).optional()
})
```
We can use it as follows:

```ts
export class MySample extends SampleComponent implements IValidated, z.infer<typeof schema>
{
  validator: Validator = new ZodValidator(this, schema)

  username!: string
  rating?: number

  submit () {
    this.validator.validate()
    ...
  }

  override onUpdated(event: UpdateEvent) {   
    this.validator.revalidate(event)
  }

 view () {
   return div (
     formField({
      target: this
      prop: () => this.usernmae,
      inputFn: inputText
    }),
    button({ onClick: () => this.submit()})
    ... // define other formField etc.
  }
}
```

Let's break this down:

* `Validator` is a domeleon base class that orchtestrates validating your components
  * Recursively validates your components by calling `validateSync` on each validator
  * Triggers `onValidate` on your component, to handle custom asynchronous validations
  * Collates all the validations per property, and maintains an overall validation `state`
 * `ZodValidator` implements `validateSync` to validate your component's properties with your `Zod` schema

The `onUpdated` override gives incremental feedback to the user, by revalidating the component, as they adjust their form.

### Async Validation

We can also provide custom async validation via overriding the `onValidate` method. This is useful for asynchronous valdation, or validation that applies to the form as a whole, rather than a particular property.

For example:

```ts

  override async onValidate() {
    const result = await ...    // call server here
    if (this.username == ...) { // check if username is unique
      return []                 // no validation errors
    }
    return [{
      property: 'username',
      messages: ['Username is not unique'],
      value: this.username
    }]
  }
```
Your component will receive two validation update events, first a synchronous one, to immediately give feedback to your users, followed by the asynchronous one.

### `validator.state`

A `validator`'s `state` is of type `ValidationState`:

```ts
"unvalidated" | // yet to be valiated
"validating" |  // in the process of validation, no validation failed yet
"invalid" |     // at least one synchronous or asynchronous validation failed
"valid"         // determine valid, all async validatio complete
```

> `validator.state` is especially highlighted in the `inspector`.

## Related Topics

* [Forms](./forms.md)
* [Inspector](./inspector.md)