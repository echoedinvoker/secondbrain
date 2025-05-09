---
date: 2025-05-08
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Error handling for fetch failing

```vue
<template>
    <section class="grid grid-cols-2 gap-8">
      <template v-if="loading">
        <div v-for="i in 4" :key="i">
          <LoadingEventCard />
        </div>
      </template>
      <template v-else>
        <template v-if="events.length">
          <EventCard ... />
        </template>
        <template v-else>
          <div class="col-span-2 text-center text-gray-500">
            No events available
          </div>
        </template>
      </template>
    </section>
</template>

<script setup lang="ts">
...
const events = ref<Event[]>([])
const loading = ref(false)
const fetchEvents = async () => {
  loading.value = true
  try {
    const response = await fetch('http://localhost:3001/events')
    events.value = await response.json()
  } finally {
    loading.value = false
  }
}

onMounted(() => fetchEvents())
</script>
```

Although the above component handles states for fetch loading and empty fetch results, if the fetch fails, there will be no prompt and the user will not be able to retry. Therefore, we need to add an error state to handle this issue.

```js
...
const error = ref(null) // create an error state to store the error object, if it's not null, it means the fetch failed and we can show an error message on the template

const fetchEvents = async () => {
  loading.value = true
  error.value = null // initialise error state to null whenever we start/restart fetching
  try {
    const response = await fetch('http://localhost:3001/events') 
    events.value = await response.json()

  // when the fetch fails, we store the error object in the error state
  } catch (err) {
    error.value = err

  } finally {
    loading.value = false
  }
}
```

Then, we can show the error message in the template conditionally based on the error state. We can also add a button to retry fetching the events *without reloading the page*. 

```vue
<template>
  <template v-if="error"> <!-- if the error state is not null, we show the error message and a retry button -->
    <SectionCard>
      <div class="space-y-4 items-center flex flex-col">
        <div class="text-red-500">Could not load events at the moment. Please try again later.</div>
        <Button @click="fetchEvents" label="Retry now"></Button>
      </div>
    </SectionCard>
  </template>
  <template v-else> <!-- if the error state is null, we show the loading state or the events -->
    <!-- original template content -->
  </template>
</template>

...

```
`items-center` is a Tailwind CSS utility class that centers items along the cross axis (vertically in this case) in a flex container. It is used to align the content of the error message and the retry button in the center of the card. It will also effect the button width to avoid the button being too wide. 
