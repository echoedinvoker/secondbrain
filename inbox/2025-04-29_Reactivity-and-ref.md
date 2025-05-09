---
date: 2025-04-29
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Reactivity and ref

Reactivity means that when a value changes, the UI updates automatically. In Vue 3, reactivity is achieved using the `ref` API.

```html
<!DOCTYPE html>
<html lang="en">

<head> ... </head>

<body>
  <div id="app">
    <div>
      {{message}} <!-- note, if ref is used in the template, we don't need to use .value -->
    </div>
  </div>
  <script>
     const { createApp, ref } = Vue;
     //                 ^^^ deconstructing the Vue object to get `ref`
     const App = {
       setup() {
         const message = ref('Hello') // creating a reactive reference with initial value 'Hello'

         setTimeout(() => {
           message.value = 'Bye!'
           //     ^^^^^^ when we want to change the value of a ref, we use `.value`
         }, 2000)

        
         return {
           message // returning the ref from the setup function, so it can be used in the template
         }
       },
     };

     createApp(App).mount('#app');
  </script>
</body>

</html>

