# General

Code style must follow the [MDN guidelines for javascript](https://developer.mozilla.org/en-US/docs/MDN/Writing_guidelines/Writing_style_guide/Code_style_guide/JavaScript). On this page, MDN also describes some other general ways to use certain functions, comments, etc., which aren't covered by the Prettier plugin.

MDN uses Prettier as a starting point for most of their guidelines. For a guide on how to set up Prettier for a project, you can take a look at the guide in the [phpstorm documentation](https://www.jetbrains.com/help/phpstorm/prettier.html#ws_prettier_configure).

## File organisation

The location of files depends on the framework/style within the framework, so for that part, there are no general rules in place.

Files should be split up logically and named accordingly. Here are a few rules/guidelines for how to separate files:

1. Classes should always be placed into a separate file. The name of the class should correspond with the name of the file. For example, a class with the name Color should be placed in a file called `color.js`.
2. Functions that are not within a class are grouped together with similar functions and given a generic name. For example, two separate functions, both enforcing different aspect ratios, are grouped in a file called `aspect-ratio.js`. In general these functions are the ones that in PHP would fall under helper classes or traits.

File names should always be written in lowercase letters and may contain underscores (\_) and hyphens (-). The general preference is to use hyphens for spaces in favor of underscores.

