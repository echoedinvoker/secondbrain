---
date: 2025-04-29
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Conditional Style Class

With reactive references and v-bind directive, we can conditionally apply classes to elements in Vue 3.

```html
<!DOCTYPE html>
<html lang="en">

<head>
  ...
  <style type="text/css">
    body { ... }

    /*Create two classes that will be used to style the text based on the value of canDrive.*/
    .can { 
      color: green;
    }
    .cannot {
      color: red;
    }
  </style>
   <script src="https://unpkg.com/vue@3.4.0"></script> 
</head>

<body>
  <div id="app">

    <!--We can use the v-bind directive to bind the class attribute and conditionally apply classes based on the value of canDrive by-->
    <!--using the object syntax. The keys are the class names and the values are the conditions that determine whether the class should be applied or not.-->
    <div :class="{
      can: canDrive,
      cannot: !canDrive
    }">
      {{ canDrive }}
    </div>

  </div>
  <script>
     const { createApp, ref } = Vue;
     const App = {
       setup() {
         const canDrive = ref(false) // This is a reactive reference that will be used to determine if the user can drive or not.

         // Simulate an async operation to determine if the user can drive
         setTimeout(() => {
           canDrive.value = true
         }, 2000)

        
         return {
           canDrive, // remember to return the reactive reference so it can be used in the template
         }
       },
     };

     createApp(App).mount('#app');
  </script>
</body>

</html>

