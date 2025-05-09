---
date: 2025-05-06
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Minimum fetch codes

Here we demonstrate how to use fetch to retrieve data from the backend when the component is loaded and implement it with minimal code (no error handling, no loading state).

```vue
<script setup>
  import { onMounted, ref } from 'vue';

  const events = ref([])

  const fetchEvents = async () => {
    const response = await fetch('http://localhost:3001/events')
    events.value = await response.json()
    //             ^^^^^ .json() is also a promise, don't forget to await it
  }

  onMounted(() => fetchEvents())
</script>
```

`fetch` is a built-in JavaScript function that allows you to make network requests in any JavaScript environment, including the browser and Node.js. So you don't need to install any additional libraries for this.
