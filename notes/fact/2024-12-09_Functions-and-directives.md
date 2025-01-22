---
date: 2024-12-09
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# Functions and directives

This topic basically introduces the contents of [this official documentation page](https://tailwindcss.com/docs/functions-and-directives).

Many people do not like Tailwind CSS because there are a lot of utility classes in the code. However, we can actually use functions and directives to help set some global styles and better organize our custom styles to reduce the number of utility classes in the code.


## Directives

In the startup of Tailwind CSS, we will need to add the following content:

```css
/* input.css  */

/* We use `@tailwind` directive to inject Tailwind's base, components, and utilities styles */
/* They will be compiled to CSS and injected into the output CSS file */

/**
 * This injects Tailwind's base styles and any base styles registered by
 * plugins.
 */
@tailwind base;  /* such as headings, paragraphs, etc. */

/**
 * This injects Tailwind's component classes and any component classes
 * registered by plugins.
 */
@tailwind components;  /* such as buttons, form elements, etc. */

/**
 * This injects Tailwind's utility classes and any utility classes registered
 * by plugins.
 */
@tailwind utilities;  /* such as margin, padding, text color, etc. */

```

We can write custom styles in the same file and use `@layer` directive to tell Tailwind which “bucket” a set of custom styles belong to. Valid layers are base, components, and utilities.

```html
~/D/g/s/t/function-and-directive > cat index.html 
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="css/style.css">
  <title>Simple Tailwind Starter</title>
</head>
<body>

  <!-- add h1 and h2 tag for testing purpose -->
  <h1>Tailwind CSS Starter!</h1>
  <h2>Happy coding!</h2>

</body>
</html>
```
```css
/* input.css  */

@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {  /* means styles inside this block is belong to base layer, such as headings, paragraphs, etc. and they will also be compiled to CSS and injected into the output CSS file */

  /* define custom styles (global) */
  h1 {
    font-size: 2rem;
  }
  h2 {
    font-size: 1.5rem;
  }
}

```

Now, we can check the browser to see the result, text of `h1` and `h2` should be larger than the default size and we don't need to add any class to them.

If we want to use Tailwind CSS utility classes to style the `h1` and `h2` tags, we can use `@apply` directive to apply utility classes to custom styles.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  h1 {
    font-size: 2rem;
    @apply text-red-50;  /* use `@apply` directive to apply Tailwind CSS utility classes to custom styles */
  }
  h2 {
    font-size: 1.5rem;
  }
}


```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  h1 {
    font-size: 2rem;
    @apply text-red-50;
  }
  h2 {
    font-size: 1.5rem;
  }
}

/* if we want to create a high-level class like `.btn-blue` in bootstrap, which is suitable to be put in the `components` layer */
@layer components {
  .btn-blue {
    @apply bg-blue-500 text-white font-bold py-2 px-4 rounded hover:bg-blue-700;
  }
}

/* we can also just create a high-level class directly without assign to any layer, which still works as usual */
.btn-red {
   @apply bg-red-500 text-white font-bold py-2 px-4 rounded hover:bg-red-700;
/* ^^^^^^ we can still use `@apply` directive to apply Tailwind utility classes here */
}

```

## Functions

**theme()**

We can use `theme()` function to access values from the theme object in tailwind.config.js in our custom CSS.

```js
/* tailwind.config.js */

/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./*.html"],
  theme: {
    extend: {
      
      // we define a custom spacing value here
      spacing: {
        128: '32rem',
      }

    },
  },
  plugins: [],
}
```

```css
/* input.css */

@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  h1 {
    font-size: 2rem;
    @apply text-red-50;
  }
  h2 {
    font-size: 1.5rem;
  }
}

@layer components {
  .btn-blue {
    @apply bg-blue-500 text-white font-bold py-2 px-4 rounded hover:bg-blue-700;
  }
}

.btn-red {
   @apply bg-red-500 text-white font-bold py-2 px-4 rounded hover:bg-red-700;
}

.content-area {
  @apply bg-green-200;
  height: theme('spacing.128');
  /*      ^^^^^^^^^^^^^^^^^^^^ we can use function `theme()` to get the value from theme object in tailwind.config.js */
}

```

**screen()**

We can use `screen()` function to access breakpoints value of Tailwind CSS.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  h1 {
    font-size: 2rem;
    @apply text-red-50;
  }
  h2 {
    font-size: 1.5rem;
  }
}

@layer components {
  .btn-blue {
    @apply bg-blue-500 text-white font-bold py-2 px-4 rounded hover:bg-blue-700;
  }
}

.btn-red {
   @apply bg-red-500 text-white font-bold py-2 px-4 rounded hover:bg-red-700;
}

.content-area {
  @apply bg-green-200;
  height: theme('spacing.128');
}

@media screen(sm) {
    /* ^^^^^^^^^^ we directly use the `screen()` function to access the breakpoints value of Tailwind CSS */
    /*            sometimes we may not know the exact value of the breakpoint, so this function is very useful in this case */
                  
  body {
    @apply bg-black text-white;
  }
}

```
