---
date: 2025-05-07
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Empty state

```vue
<template>
    <section class="grid grid-cols-2 gap-8">
      <template v-if="loading"> ... </template>
      <template v-else>
        <EventCard v-for="event in events" :key="event.id" :title="event.title" :when="event.date"
        :description="event.description" @register="$emit('register', event)" />
      </template>
    </section>
</template>

...

```
The above codes render multiple EventCards, but if the fetch result is empty, the original place where EventCard was rendered will become blank. In this case, the user will not know what happened. Therefore, when the fetch result is empty, we can display some prompt text to let the user know that there are currently no EventCards to display.

```vue
<template>
    <section class="grid grid-cols-2 gap-8">
      <template v-if="loading">
        <div v-for="i in 4" :key="i">
          <LoadingEventCard />
        </div>
      </template>
      <template v-else>
        <template v-if="events.length"> <!-- If there are events, render them -->
          <EventCard v-for="event in events" :key="event.id" :title="event.title" :when="event.date"
          :description="event.description" @register="$emit('register', event)" />
        </template>
        <template v-else> <!-- If there are no events, render a message -->
          <div class="col-span-2 text-center text-gray-500">
            No events available
          </div>
        </template>
      </template>
    </section>
</template>

...

```
In the above code, we added a check for `events.length` to determine if there are any events to display. If there are no events, we render a message indicating that no events are available. And we use `template` to avoid add extra elements to the DOM. This way, we can provide a better user experience by informing the user about the empty state of the EventCard list.
