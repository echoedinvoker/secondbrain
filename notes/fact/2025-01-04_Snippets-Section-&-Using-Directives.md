---
date: 2025-01-04
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss|tailwindcss]]"
---

# Snippets Section & Using Directives

After [[2024-12-26_Hero-section-and-background-image|hero section]], we'll create a snippets section.

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8" />
  <meta http-equiv="X-UA-Compatible" content="IE=edge" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Bai+Jamjuree:wght@400;600&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="css/style.css" />
  <title>Simple Tailwind Starter</title>
</head>

<body>
  <section id="hero">
    <div class="max-w-6xl mx-auto text-center mb-40 px-10 pt-16">
           <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ -->
      <img src="images/logo.svg" alt="" class="mx-auto my-20">
      <h3 class="mb-8 text-4xl md:text-5xl font-bold text-darkGrayishBlue">
     <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ -->
        A history of everything you copy
      </h3>
      <p class="max-w-3xl mx-auto mb-10 text-2xl text-grayishBlue">
        Clipboard allows you to track and organize everything you copy. Instantly access your clipboard on all your
        devices.
      </p>
      <div
        class="flex flex-col md:flex-row items-center justify-center w-full space-y-6 md:space-y-0 md:space-x-4 text-xl text-white">
        <a href="#" class="p-4 px-8 rounded-full shadow-lg bg-strongCyan duration-200 hover:opacity-80">
          Download for iOS
        </a>
        <a href="#" class="p-4 px-8 rounded-full shadow-lg bg-lightBlue duration-200 hover:opacity-80">
          Download for Mac
        </a>
      </div>
    </div>
  </section>

  <!-- Snippets Section -->
  <section id="snippets">
    <div class="max-w-6xl mx-auto text-center my-20 px-10">
          <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Using the same classes as the hero section for alignment and spacing -->
          <!--                               to make section containers consistent                                -->
      <h3 class="mb-8 text-4xl md:text-5xl font-bold text-darkGrayishBlue">
     <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ every h3 style is exactly the same in this project -->
        Keep track of your snippets
      </h3>
      <p class="max-w-3xl mx-auto mb-24 text-xl leading-9 text-center text-grayishBlue">
        Clippboard instantly stores any item you copy in the cloud, meaning you can access your snippets immediately on
        all your devices. Our Mac and iOS apps will help you organize everything.
      </p>
    </div>
  </section>
</body>

</html>

```

We create new section `snippets` and you can see that portions of the code are repeated. We can use Tailwind CSS directives to prevent repeating the same classes.

```css
/* input.css */

@tailwind base;
@tailwind components;
@tailwind utilities;

/* because h3 is belong to base layer, we can use @layer directive to change the default style of h3 in base layer */
@layer base {
  h3 {
    @apply mb-8 text-4xl md:text-5xl font-bold text-darkGrayishBlue;  /* directly copy from index.html */
  }
}

/* create custom classes */
.section-container {
  @apply max-w-6xl mx-auto text-center;
/*^^^^^^ for using tailwind classes */
}
.button-container {  /* although no mention this above, but we'll use the same button style in the future, so create this custom class */
  @apply flex flex-col md:flex-row items-center justify-center w-full space-y-6 md:space-y-0 md:space-x-4 text-xl text-white;
}

body {
  background-image: url('../images/bg-header-desktop.png');
  background-repeat: no-repeat;
  background-size: contain;
}

@media (max-width: 576px) {
  body {
    background-image: url('../images/bg-header-mobile.png');
  }
}
```

Then, we can refactor our index.html file:

```html
<body>
  <section id="hero">
    <div class="section-container mb-40 px-10 pt-16">
          <!-- ^^^^^^^^^^^^^^^^^ use custom class -->
      <img src="images/logo.svg" alt="" class="mx-auto my-20">
      <h3> <!-- default style of h3 is changed, so we don't need to give any class to it now -->
        A history of everything you copy
      </h3>
      <p class="max-w-3xl mx-auto mb-10 text-2xl text-grayishBlue">
        Clipboard allows you to track and organize everything you copy. Instantly access your clipboard on all your devices.
      </p>
      <div class="button-container">
            <!--  ^^^^^^^^^^^^^^^^ use custom class -->
        <a href="#" class="p-4 px-8 rounded-full shadow-lg bg-strongCyan duration-200 hover:opacity-80">
          Download for iOS
        </a>
        <a href="#" class="p-4 px-8 rounded-full shadow-lg bg-lightBlue duration-200 hover:opacity-80">
          Download for Mac
        </a>
      </div>
    </div>
  </section>

  <!-- Snippets Section -->
  <section id="snippets">
    <div class="section-container my-20 px-10">
           <!-- ^^^^^^^^^^^^^^^^^ use custom class again, it make the section containers consistent easier -->
      <h3> <!-- remove all classes of h3 -->
        Keep track of your snippets
      </h3>
      <p class="max-w-3xl mx-auto mb-24 text-xl leading-9 text-center text-grayishBlue">
        Clippboard instantly stores any item you copy in the cloud, meaning you can access your snippets im mediately on all your devices. Our Mac and iOS apps will help you organize everything.
      </p>
    </div>
  </section>
</body>

</html>

```

Many people think that Tailwind CSS will make HTML messy, but the example above is a good use of directives and custom classes to simplify the classes in HTML and make it cleaner.

> However, it is important to only do this when you actually find combinations of Tailwind CSS classes that truly repeat often. Do not overuse directives and custom classes to do the simplification, otherwise you will lose the advantages of Tailwind CSS.
