# Functions

## General best practices for functions

The [PHP / laravel page](https://webwhales.teamwork.com/spaces/programmeren/page/5148-functions) about functions includes a few common best practices for writing functions. *Small functions*, *One purpose, Few arguments & duplicate code* are especially relevant as they apply to Javascript as well and should be taken into account when writing javascript code.

## Naming convention

Reference: [https://developer.mozilla.org/en-US/docs/MDN/Writing\_guidelines/Writing\_style\_guide/Code\_style\_guide/JavaScript#function\_names](https://developer.mozilla.org/en-US/docs/MDN/Writing_guidelines/Writing_style_guide/Code_style_guide/JavaScript#function_names)

Function names should be writen in camel case, starting with a lower case character. Function names should briefly explain the function or be obvious and literal in any possible interpretation of what they do. Functions should always be declared with the function keyword where possible.

 **Wrong**

```javascript
function StartingOfWithUpperCase() {
  // This function is starting with a uppercase letter where it should have been lowercase.
}

function not_writing_in_camel_case() {
  // This function is written without using camel case.
}

function doIt() {
  // This function name is unclear of it's purpose.
}

let test = (a) => {
  // This function is unclear and declared without the function keyword.
};
```

  **Correct**

```javascript
// Name describes the action and is written in camelcase
function changeTextToInput(input, element) {
  element.innerHTML = input.value;
}

// When a variable is used to assign the function to, 
// the variable name should be the descriptive part.
const logParameterToConsole = function (a) {
  console.log(a);
};
```

## Anonymous functions

Reference: [https://developer.mozilla.org/en-US/docs/MDN/Writing\_guidelines/Writing\_style\_guide/Code\_style\_guide/JavaScript#function\_declarations](https://developer.mozilla.org/en-US/docs/MDN/Writing_guidelines/Writing_style_guide/Code_style_guide/JavaScript#function_declarations)

Anonymous function declarations are opposite from the regular function declaration. Here, we prefer to use the arrow functions instead of the function keyword.

 **Wrong**

```javascript
const array1 = [1, 2, 3, 4];
const sum = array1.reduce(function (a, b) {
  return a + b;
});

```

 **Correct**

```javascript
const array1 = [1, 2, 3, 4];
const sum = array1.reduce((a, b) => a + b);

```

