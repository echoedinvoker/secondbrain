---
date: 2024-11-29
type: fact
aliases:
  -
hubs:
  - "[[Tailwindcss]]"
---

# Try to try mimic css styles with tailwind utility classes

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Webpack Tailwind Project</title>
    <style>
      body {
        margin-top: 200px !important;
      }
      .alert {
        display: flex;
        max-width: 24rem;
        margin: 0 auto;
        padding: 1.5rem;
        border-radius: 0.5rem;
        background-color: #fff;
        box-shadow: 0 20px 25px -5px rgba(0, 0, 0, 0.1),
          0 10px 10px -5px rgba(0, 0, 0, 0.04);
      }
      .alert-logo-wrap {
        flex-shrink: 0;
      }
      .alert-logo {
        height: 3rem;
        width: 3rem;
      }
      .alert-body {
        margin-left: 1.5rem;
        padding-top: 0.25rem;
      }
      .alert-title {
        color: #1a202c;
        font-size: 1.25rem;
        line-height: 1.25;
        font-weight: 500;
      }
      .alert-message {
        color: #718096;
        font-size: 1rem;
        line-height: 1.5;
      }
    </style>
  </head>
  <body>
    <div class="alert">
      <div class="alert-logo-wrap">
        <img class="alert-logo" src="warning.svg" alt="alert" />
      </div>
      <div class="alert-body">
        <h4 class="alert-title">Are You Sure?</h4>
        <p class="alert-message">You are about to delete a post</p>
      </div>
    </div>
  <div id="app"></div>
</body>

</html>

```

![regular-css-styles.png](../assets/imgs/regular-css-styles.png)


Then we use tailwind in index.js to mimic the same styles:

```js
// index.js

import './styles.css';

document.getElementById('app').innerHTML = `
  <div class="flex items-center p-6 max-w-sm mx-auto bg-white rounded-xl shadow-md space-x-4">
    <img class="w-12 h-12" src="warning.svg" alt="">
    <div>
      <div class="text-xl font-medium text-black">Are You Sure?</div>
      <p class="text-slate-500">You are about to delete a post</p>
    </div>
  </div>
`;

```

![mimic-styles.png](../assets/imgs/mimic-styles.png)

Because we do not know the exact content of each utility class, we only have a general idea of their functions, so of course there will be some differences as shown in the above diagram.

However, the advantage of Tailwind CSS is that we can immediately adjust the details of each utility class in the code and see instant changes on the screen. This development approach allows us to complete layout design more quickly.

In addition, we can use config to make changes to the actual css content of each utility class.
```html
