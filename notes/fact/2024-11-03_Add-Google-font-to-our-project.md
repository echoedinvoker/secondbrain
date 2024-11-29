---
date: 2024-11-03
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Add Google font to our project

If you need to use a font other than the built-in font, you can use Google font, here using Open Sans and Anton as examples.
Additional assets can be imported in app.html.

```html src/app.html 

<!doctype html>
<html lang="en">

<head>
  <meta charset="utf-8" />
  <link rel="icon" href="%sveltekit.assets%/favicon.png" />

  <!-- copy paste from google font website -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Open+Sans:ital,wght@0,300..800;1,300..800&display=swap" rel="stylesheet" />
  <link href="https://fonts.googleapis.com/css2?family=Anton&display=swap" rel="stylesheet" />
  <!-- end of copy paste -->

  <meta name="viewport" content="width=device-width, initial-scale=1" />
  %sveltekit.head%
</head>

<body data-sveltekit-preload-data="hover">
  <div style="display: contents">%sveltekit.body%</div>
</body>

</html>

```

```css src/app.css 


body {
  font-family: "Open Sans", sans-serif;   /* Open Sans font is NOT built in, need to import in app.html */
}

/* TYPOGRAPHY */
h1 {
  font-size: 100px;
  font-family: "Anton", sans-serif;   /* Anton font is NOT built in, need to import in app.html */
  line-height: 1.1;
  /* Adjust as needed for spacing */
  font-weight: bold;
}

h2 {
  font-size: 60px;
  font-family: "Anton", sans-serif;
  line-height: 1.2;
  font-weight: bold;
}

h3 {
  font-size: 22px;
  line-height: 1.3;
  font-family: "Anton", sans-serif;
  font-weight: normal;
}

h4 {
  font-family: "Anton", sans-serif;
  font-size: 20px;
  line-height: 1.3;
  font-weight: normal;
}

p,
li {
  font-size: 20px;
  line-height: 1.4;
}

h1,
h2,
h3,
h4,
h5,
h6,
p {
  margin: 0;
}

```

```svelte src/routes/+layout.svelte 

<script lang="ts">
  import './../app.css';  // import CSS file

  let { children } = $props();
</script>

{@render children()}

```
