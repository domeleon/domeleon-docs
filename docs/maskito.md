# Maskito

Domeleon has wrappers for `maskito`'s mask editors. The built in `number` input in HTML is terrible; use `maskito` instead. It's far cleaner, more powerful, and works uniformly across all browsers.

```ts
import { inputMask, inputNumber } from 'domeleon/maskito'
```

You can use it exactly like you would `inputText` or `inputRange`; the `inputProps` exposes the `maskito` object model to configure it. Here's an example of using `inputNumber` with a `formField`:

```ts
  formField({   
    prop: () => this.rating, 
    inputFn: inputNumber, 
    inputProps: {
      numberParams: {
        maximumFractionDigits: 1,
        decimalSeparator: '.'
      }
    }
  })
```

## Related

* [Forms](./forms.md)