---
date: 2024-12-26
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# Setup new project clipboard

1. clone project template from GitHub

```bash
git clone https://github.com/bradtraversy/simple-tailwind-starter.git clipboard
```

2. get assets resource from GitHub and copy to project

```bash
git clone https://github.com/bradtraversy/tailwind-course-projects.git

# copy assets to clipboard project

~/D/g/s/t/clipboard > tree -I 'node_modules'
.
├── images  # copy from tailwind-course-projects/clipboard/images
│   ├── bg-header-desktop.png
│   ├── bg-header-mobile.png
│   ├── favicon-32x32.png
│   ├── icon-blacklist.svg # we'll use <img src ... to show this image, but in production it's better to use inline svg in the HTML file directly
│   ├── icon-facebook.svg
│   ├── icon-instagram.svg
│   ├── icon-preview.svg
│   ├── icon-text.svg
│   ├── icon-twitter.svg
│   ├── image-computer.png
│   ├── image-devices.png
│   ├── logo-google.png
│   ├── logo-hp.png
│   ├── logo-ibm.png
│   ├── logo-microsoft.png
│   ├── logo.svg
│   └── logo-vector-graphics.png
├── index.html
├── input.css
├── package.json
├── package-lock.json
├── readme.md
└── tailwind.config.js
```

3. Adjust `tailwind.config.js` for custom breakpoints and extend colors and fonts

```ts
// tailwind.config.js

module.exports = {
  content: ['./*.html'],
  theme: {
    // override default breakpoints
    screens: {
      sm: '480px',
      md: '768px',
      lg: '976px',
      xl: '1440px',
    },

    // extend colors and fonts
    extend: {
      colors: {
        strongCyan: 'hsl(171, 66%, 44%)',
        lightBlue: 'hsl(233, 100%, 69%)',
        darkGrayishBlue: 'hsl(210, 10%, 33%)',
        grayishBlue: 'hsl(201, 11%, 66%)',
      },
      fontFamily: {
        sans: ['Bai Jamjuree', 'sans-serif'],
        //      ^^^^^^^^^^^^ need to add this font in index.html
      },
    },
  },
  plugins: [],
}
```

4. Get links from Google Fonts and add to `index.html`

![bai-jamjuree-copy.png](../assets/imgs/bai-jamjuree-copy.png)

```html
<!-- index.html -->

<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8" />
  <meta http-equiv="X-UA-Compatible" content="IE=edge" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />

  <!-- copy paste links from google fonts -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Bai+Jamjuree:wght@400;600&display=swap" rel="stylesheet">

  <link rel="stylesheet" href="css/style.css" />
  <title>Simple Tailwind Starter</title>
</head>

<body>
  <h1 class="text-4xl mb-4">Welcome</h1>
  <p>Start building your Tailwind project</p>
</body>

</html>

```

