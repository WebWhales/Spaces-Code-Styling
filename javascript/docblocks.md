# DocBlocks

## DocBlocks & type hinting in JavaScript

DocBlocks (or JSDoc comments) are used to describe **types in JavaScript** code without needing TypeScript. They help with:

- **Type hinting** and autocompletion in IDEs
- **Better code readability and navigation**
- **Error prevention** through editor-level checks

DocBlocks are placed above functions, variables, or objects using `/** ... */` block comments.

## Type hinting examples

### Basic function and parameters

```php
/**
 * @param {number} price
 * @param {number} taxRate
 *
 * @returns {number} Total price including tax
 */
function calculateTotal(price, taxRate) {
  return price + price * (taxRate / 100);
}
```

You may add a description to a parameter or return value. Avoid descriptions that don't really add value and just describe what can already be understood from reading the function declaration.

### Arrays

In JavaScript, arrays can only represent lists of other types (such as objects, strings, numbers, etc.). However, it can be helpful to document the types of the expected variables within the array.

```php
/**
 * @type {number[]}
 */
const userIds = [101, 102, 103];

/**
 * @type {{ id: number, name: string, isActive: boolean }[]}
 */
const users = [
  { id: 1, name: 'John', isActive: true },
  { id: 2, name: 'Nick', isActive: false },
];
```

### Object structures

```javascript
/**
 * @type {{id: number, name: string, price: number, inStock: boolean}}
 */
const product = {
  id: 1,
  name: 'Laptop',
  price: 999.99,
  inStock: true,
};

/** @type {Object.<string number="">} */
const pricesBySku = {}</string>
```

With the following notation, you can add more detail to specific properties of the object:

```javascript
/**
 * @type {Object} product
 * @type {number} product.id
 * @type {string} product.name
 * @type {number} product.price
 * @type {boolean} product.inStock - Product availability
 */
const product = {
  id: 1,
  name: 'Laptop',
  price: 999.99,
  inStock: true,
};
```

When working with objects, you can use `@typedef` to define a reusable shape. These shapes can also be referenced across files.

```javascript
/**
 * @typedef {Object} Product
 * @property {number} id
 * @property {string} name
 * @property {number} price
 * @property {boolean} inStock - Product availability
 */

/** @type {Product} */
const product = {
  id: 1,
  name: 'Laptop',
  price: 999.99,
  inStock: true,
};
```

### 
Importing object types from packages

```javascript
/**
 * @param {import('express').Request} req
 * @param {import('express').Response} res
 */
function getUser(req, res) {
  res.send({ user: 'Alice' });
}

/** @type {import('laravel-precognition-vue-inertia/dist/types').Form} */
const form = inject('form')
```

Imported object types can also be written using `@typedef`:

```javascript
/**
 * @typedef {import('express').Request} Request
 * @typedef {import('express').Response} Response
 * @typedef {import('laravel-precognition-vue-inertia/dist/types').Form} Form
 */

/**
 * Express route handler
 * @param {Request} req
 * @param {Response} res
 */
function getUser(req, res) {
  res.send({ user: 'Alice' });
}

/** @type Form */
const form = inject('form')
```

## Recommended Practices

| Type | Notation |
|---|---|
| Simple variable type | `@type {string}` |
| Simple structured object | `@type {{id: number, name: string}}` |
| Array of known variable types | `@type {number[]}` |
| Reusable object shape | `@typedef {Object} ObjectTypeName` |
| Array of structured objects | `@type {{id:number, name:string}[]}`  `@type {ObjectTypeName[]}` |
| Import a type from a package | `@type {import('package').Type}` |

## When to use JavaScript docBlocks?

When writing functions that are going to be used outside the current file, we aim to always write a docBlock at function declaration level.

For functions that are only being used within the current file, or for inline code, we only use docBlocks when the type or structure of a variable is not clear when looking at the code and/or can not be recognized by our IDE.

## Sources

- [https://jsdoc.app/](https://jsdoc.app/)
- [https://jsdoc.app/tags-type](https://jsdoc.app/tags-type)
- [https://jsdoc.app/tags-typedef](https://jsdoc.app/tags-typedef)
- [https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html](https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html)
- [https://ellodave.dev/blog/article/typing-js-files-with-jsdoc/](https://ellodave.dev/blog/article/typing-js-files-with-jsdoc/)
- [https://dev.to/darkmavis1980/how-to-define-objects-with-type-definitions-in-jsdoc-comments-23n1](https://dev.to/darkmavis1980/how-to-define-objects-with-type-definitions-in-jsdoc-comments-23n1)

