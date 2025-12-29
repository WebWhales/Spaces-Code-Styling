# Comments

reference: [https://developer.mozilla.org/en-US/docs/MDN/Writing\_guidelines/Writing\_style\_guide/Code\_style\_guide/JavaScript#comments](https://developer.mozilla.org/en-US/docs/MDN/Writing_guidelines/Writing_style_guide/Code_style_guide/JavaScript#comments)

## When to add comments

Comments should be added to the code only if there's that something isn't easily decyphered from the code itself or when variable/method names aren't clear enough. Block comments are not supposed to be used within the formatting of javascript by the MDN standards, instead you should use `//` on multiple lines.

 **Wrong**

The comment is spread over multiple lines and is not adding relevant information to the context.

```javascript
let total = 0;

// For loop from 1 to 4
for (let i = 0; i < 4; i++) {
  // Add value to the total
  total += arr[i];
}

```

The comment in the example, is just the function name written out without providing new or meaningful infromation to the reader.

```javascript
closeConnection(); // Closing the connection
```

  **Correct**

```javascript
let total = 0;

// Calculate the sum of the four first elements of arr
for (let i = 0; i < 4; i++) {
  total += arr[i];
}

```

## Comments after console logs

Within javascript console logs are usually placed for debugging purposes, or to verify that data is handled correctly before and after some actions. To help clarify to other users what they can expect when viewing a console log in the code, the programmer should add a comment after the `console.log `providing additional information about it's contents.

```javascript
console.log(fruits); // [Banana, Kiwi, Orange Apple]

console.log(index + 5); // 7
```

