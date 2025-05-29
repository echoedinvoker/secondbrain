---
date: 2025-05-13
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Sync LocalStorage with Ref

We can use IIF to initialize a ref with the value from localStorage or default value in Vue.js.

```javascript
const settings = ref(() => {
  const stored = localStorage.getItem("settings"); // when key is not found, it returns null
  return stored !== null
    ? JSON.parse(stored) // because localStorage stores everything as a string
    : { theme: "light", language: "en" }; // default value
});

// then, we need to watch the ref and update localStorage whenever it changes

watch(
  settings, // when watching a ref, we don't need to use .value
  (value) => {
    localStorage.setItem("settings", JSON.stringify(value));
    //                               ^^^^^^^^^^^^^^^^^^^^^ remember that localStorage only stores strings
  },
  { deep: true } // usually we want to watch the ref deeply, so we need to set deep to true
);
```
