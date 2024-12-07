---
date: 2024-12-02
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# Configuration

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
      tailwind.config = {
        theme: {
          // below we define `screens` and `fontFamily` properties under `theme` property
          // it will replace the default values of `screens` and `fontFamily` properties
          screens: {
            sm: '550px',
            md: '800px',
            lg: '1200px',
            xl: '1440px',
            // so if you try to use `2xl:bg-orange-800` it will not work, because it is not defined here
          },
          fontFamily: {
            sans: ['Graphik', 'sans-serif'],
            serif: ['Merriweather', 'serif'],
          },

          extend: {
            // below we define `colors`, `spacing` properties under `extend` property
            // it will extend the default values of `colors` and `spacing` properties, not replace
            colors: {
              primary: '#FF5733',
              secondary: '#FFFC33',
              // so that we can use `bg-primary` and `text-secondary` classes
              // but we can still use the default colors such as `bg-black` and `text-white`
            },
            spacing: {
              5: '3.5rem',  // spacing `5` already exists, so its value will be replaced by `3.5rem`
            },
          },
        },
      }
    </script>

    <title>Customization</title>
  </head>
  <!-- Tailwind is mobile-first -->
  <body
    class="bg-black sm:bg-green-800 md:bg-primary lg:bg-yellow-800 xl:bg-purple-800 2xl:bg-orange-800"
               <!-- ^^^             ^^^   ^^^^^^^ ^^^              ^^^              ^^^^ not defined -->
               <!-- ^^^             ^^^   ^ extend color value     ^^^               -->
               <!-- ^^^             ^^^                            ^^^               -->
               <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ values are replaced by custom values -->
  >
    <div class="bg-black p-5">
      <h1 class="text-white text-xl md:text-3xl md:text-secondary xl:text-5xl">
        Tailwind is awesome
      </h1>
    </div>

    <script>
      function showBrowserWidth() {
        const width =
          window.innerWidth ||
          document.documentElement.clientWidth ||
          document.body.clientWidth

        document.querySelector('h1').innerHTML = `Width: ${width}`
      }
      window.onload = showBrowserWidth
      window.onresize = showBrowserWidth
    </script>
  </body>
</html>

<!-- Breakpoint Classes (default values)
    sm	640px	  @media (min-width: 640px) { ... }
    md	768px	  @media (min-width: 768px) { ... }
    lg	1024px	@media (min-width: 1024px) { ... }
    xl	1280px	@media (min-width: 1280px) { ... }
    2xl	1536px	@media (min-width: 1536px) { ... }
  -->

```


