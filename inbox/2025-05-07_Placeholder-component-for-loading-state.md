---
date: 2025-05-07
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Placeholder component for loading state

When fetching data, while still in a loading state, we need an indicator to let the user know the status of the fetch. A popular method is to use a placeholder component as the indicator for the loading state.

```vue
<template>
  ...
      <!-- Placeholder component for loading state -->
      <template v-if="eventsLoading">
        <div v-for="i in 4" :key="i">
          <LoadingEventCard /> <!-- Placeholder component, which should be designed to look like the actual EventCard -->
        </div>
      </template>

      <!-- Actual component to display the data -->
      <template v-else>
        <EventCard v-for="event in events" :key="event.id" :title="event.title" :when="event.date"
          :description="event.description" @register="console.log('Registered for', $event)" />
      </template>
  ...
</template>

<script setup lang="ts">
...
const events = ref<Event[]>([])
const eventsLoading = ref(false)

const fetchEvents = async () => { ... }

onMounted(() => fetchEvents())
</script>
```

We must design the style of LoadingEventCard based on the common style of EventCard, so that users do not feel abrupt when loading data.

```vue
<!-- EventCard.vue -->
<template>
  <SectionedCard>
    <template #header>{{ title }}</template>
    {{ when }}
    <template #footer>
      <p>{{ description }}</p>
      <div class="flex justify-end">
        <Button label="Register" @click="$emit('register', title)" />
      </div>
    </template>
  </SectionedCard>
</template>
...
```

You can see that there are other custom components used in EventCard, such as `SectionedCard` and `Button`. `SectionedCard` is only used to provide border and padding styles, so we can directly use it in `LoadingEventCard` and just change the content passed into each slot to a placeholder style (gray background, loading animation).

```vue
<!-- LoadingEventCard.vue -->
<template>
  <SectionedCard>
    <template #header>
      <div class="h-6 w-48 bg-gray-200 animate-pulse rounded"></div>
    </template>

    <template #default>
      <div class="space-y-4">
        <div class="h-4 w-1/4 bg-gray-200 animate-pulse rounded"></div>
      </div>
    </template>

    <template #footer>
      <div class="space-y-4">
        <div class="h-4 w-3/4 bg-gray-200 animate-pulse rounded"></div>
        <div class="h-4 w-3/4 bg-gray-200 animate-pulse rounded"></div>
        <div class="h-4 w-2/4 bg-gray-200 animate-pulse rounded"></div>
        <div class="flex justify-end items-center">
          <div class="h-8 w-24 bg-gray-200 animate-pulse rounded"></div>
        </div>
      </div>
    </template>
  </SectionedCard>
</template>

<script setup lang="ts">
import SectionedCard from '@/components/SectionedCard.vue'
</script>
```

Above, we can use the `h- w- bg-gray- animate-pulse rounded` classes of Tailwind CSS to quickly create a loading bar effect, then adjust the details to make it similar to the presentation of EventCard.

