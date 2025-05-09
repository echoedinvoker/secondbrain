---
date: 2025-05-06
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Loading state

We always need to track the loading state when fetching.

```vue
<script setup lang="ts">
  // ...

  const events = ref<Event[]>([])
  const eventsLoading = ref(false) // loading state

  const fetchEvents = async () => {
    eventsLoading.value = true // set loading state to true at the start of the request
    const response = await fetch('http://localhost:3001/events')
    events.value = await response.json()
    eventsLoading.value = false // set loading state to false when the request is done
  }

  // ...
</script>
```

However, fetching may fail, which can cause the loading state to never be set to false, so we need to use try ... finally to solve this problem.

```js
  const fetchEvents = async () => {
    eventsLoading.value = true
    try { // wrap the request in try block
      const response = await fetch('http://localhost:3001/events')
      events.value = await response.json()
    } finally { // finally block will always run, so we can set loading state to false here
      eventsLoading.value = false
    }
  }
```

