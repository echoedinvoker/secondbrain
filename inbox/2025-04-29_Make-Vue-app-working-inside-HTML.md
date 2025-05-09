---
date: 2025-04-29
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Make Vue app working inside HTML

We can run a Vue app using only one html file.

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Vue 3.4 Template</title>
  <link rel="stylesheet" href="https://unpkg.com/normalize.css/normalize.css">
  <style type="text/css">
    body {
      font-family: monospace;
      padding: 20px;
      font-size: 2em;
    }
  </style>

   <!-- Download an Vue object from CDN -->
   <script src="https://unpkg.com/vue@3.4.0"></script>

</head>

<body>
  <div id="app">
    <div>
      {{message}}  <!-- interpolation: it means that Vue app will replace this with the value of variable 'message' -->
    </div>
  </div>
  <script>
     const { createApp } = Vue; // We can deconstruct Vue object to get the createApp function, which is used to create a Vue app

     // createApp function takes an object as an argument, which is the root component of the app
     // it must have a setup function, all the logic of Vue app inside
     const App = {
       setup() {
        
         // anything used in the template (variables, methods...) should be returned from setup function
         return {
           message: 'Hello'
         }
       },
     };

     // use 'createApp' to create Vue app and mount it on the element with `app` id
     createApp(App).mount('#app');

  </script>
</body>

</html>

