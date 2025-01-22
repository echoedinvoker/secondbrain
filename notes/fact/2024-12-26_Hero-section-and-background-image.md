---
date: 2024-12-26
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# Hero section and background image

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
  <!-- Hero Section -->
  <section id="hero">
      <!-- ^^^^^^^^^ if we use regular or hamburger menu, we can navigate to the sections using the id -->
    <!-- Main Container -->
    <div class="max-w-6xl mx-auto text-center mb-40 px-10 pt-16">
           <!-- ^^^^^^^^^ make the container responsive to screen width until 6xl -->
      Hello World!
    </div>
  </section>
</body>

</html>

```

![rwd-max-w1.png](../assets/imgs/rwd-max-w1.png)

![rwd-max-w2.png](../assets/imgs/rwd-max-w2.png)

```html
<body>
  <!-- Hero Section -->
  <section id="hero">
    <!-- Main Container -->
    <div class="max-w-6xl mx-auto text-center mb-40 px-10 pt-16">
      <!-- Logo & Heading -->
      <img src="images/logo.svg" alt="" class="mx-auto my-16"> <!-- better to use svg directly, but for now we use img for more clarity -->
                                          <!-- ^^^^^^^ center the image -->
      <h3 class="mb-8 text-4xl md:text-5xl font-bold text-darkGrayishBlue"> <!-- we'll use lots of h3 with the same style, so we'll use directive to prevent repeating, but for now we just write styles -->
        A history of everything you copy
      </h3>
      <p class="max-w-3xl mb-10 text-2xl text-grayishBlue">
           <!-- ^^^^^^^^^ paragraph usually has a max width -->
        Clipboard allows you to track and organize everything you copy. Instantly access your clipboard on all your
        devices.
      </p>
    </div>
  </section>
</body>

```

![hero-clipboard.png](../assets/imgs/hero-clipboard.png)

```html
<body>
  <!-- Hero Section -->
  <section id="hero">
    <!-- Main Container -->
    <div class="max-w-6xl mx-auto text-center mb-40 px-10 pt-16">
      <!-- Logo & Heading -->
      <img src="images/logo.svg" alt="" class="mx-auto my-16">
      <h3 class="mb-8 text-4xl md:text-5xl font-bold text-darkGrayishBlue">
        A history of everything you copy
      </h3>
      <p class="max-w-3xl mb-10 text-2xl text-grayishBlue">
        Clipboard allows you to track and organize everything you copy. Instantly access your clipboard on all your
        devices.
      </p>
      <!-- Button Container -->
      <div class="flex flex-col md:flex-row items-center justify-center w-full space-y-6 md:space-y-0 md:space-x-4 text-xl text-white">
                  <!-- ^^^^^^^^^^^^^^^^^^^^                             ^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^^^^^^^^ use inherited styles to make children text with the same style -->
                  <!-- ^^^^^^^^^^^^^^^^^^^^                             ^^^^^^ ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ different gap space when col and row -->
                  <!-- ^^^^^^^^^^^^^^^^^^^^                             ^^^^^^ flexbox itself width is full to the parent -->
        <a href="#" class="p-4 px-8 rounded-full shadow-lg bg-strongCyan duration-200 hover:opacity-80">
                                                                    <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ easy transition, opacity-100 is default value so we don't need to write it -->
          Download for iOS
        </a>
        <a href="#" class="p-4 px-8 rounded-full shadow-lg bg-lightBlue duration-200 hover:opacity-80">
          Download for Mac
        </a>
      </div>
    </div>
  </section>
</body>

```
![duo-button-clip1.png](../assets/imgs/duo-button-clip1.png)

![duo-buttons-clip2.png](../assets/imgs/duo-buttons-clip2.png)

We want to set background image, and we use two different images for mobile and desktop.


```css
/* input.css  */
/* when we want to set custom style, we come to this file */

@tailwind base;
@tailwind components;
@tailwind utilities;

/* background image for desktop */
body {
  background-image: url('../images/bg-header-desktop.png');  /* there is no class to define background image, so we use native css */
  background-repeat: no-repeat;
  background-size: contain;
}

/* background image for mobile */
@media (max-width: 576px) {
  body {
    background-image: url('../images/bg-header-mobile.png');
  }
}
```

![clip-bg-img1.png](../assets/imgs/clip-bg-img1.png)

![clip-bg-img2.png](../assets/imgs/clip-bg-img2.png)

