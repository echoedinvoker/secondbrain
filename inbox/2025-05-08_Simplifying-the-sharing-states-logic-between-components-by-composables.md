---
date: 2025-05-08
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Simplifying the sharing states logic between components by composables

```vue
<!-- EventList.vue -->
<template>
...
          <EventCard v-for="event in events" :key="event.id" :title="event.title" :when="event.date"
          :description="event.description" @register="$emit('register', event)" />
...
</template>

<script setup lang="ts">
...
defineEmits(['register'])

const events = ref<Event[]>([])
...
</script>
```

```vue
<!-- App.vue -->
<template>
...
    <EventList @register="handleRegister" />
...
    <BookingList ref="bookingList" />
...
</template>

<script setup lang="ts">
...
const bookingList = ref<InstanceType<typeof BookingList> | null>(null)

const handleRegister = (event: Event) => {
  bookingList.value && bookingList.value.bookEvent(event)
}
</script>
```

```vue
<!-- BookingList.vue -->
<template> ... </template>

<script setup lang="ts">
...
const bookings = ref<Booking[]>([])
...
const bookEvent = async (event: Event) => {
  const newBooking = {
    id: Date.now().toString(),
    userId: 1,
    eventId: event.id,
    eventTitle: event.title,
    status: 'pending'
  }

  if (bookings.value.some((booking) => booking.eventId === event.id && booking.status === 'confirmed')) {
    alert('You have already booked this event.')
    return
  }

  bookings.value.push(newBooking)

  try {
    const response = await fetch('http://localhost:3001/bookings', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        ...newBooking,
        status: 'confirmed',
      })
    })

    if (!response.ok) throw new Error('Failed to book event')

    const booking = await response.json()
    const index = bookings.value.findIndex((b) => b.id === newBooking.id)
    bookings.value[index] = booking

  } catch (error) {
    console.error('Error booking event:', error)
    bookings.value = bookings.value.filter((booking) => booking.id !== newBooking.id)
  }
}
...
defineExpose({
  bookEvent
})
</script>
```

In the code above, the `EventList` component passes the registered event to the `App` component through the `$emit` event, and then the `App` component passes the event to the `BookingList` component's `bookEvent` method for processing. The logic of shared state has become a bit complicated, and in real projects, this situation is more common and likely to be even more complex. This kind of shared state logic can make our components difficult to maintain. At this point, we can consider using composables to simplify this logic.

For this case, we can create a composable that handles all the logic related to bookings as follows:

```ts
// src/composables/useBookings.ts (new file)
import { onMounted, ref } from 'vue'
import type { Booking, Event } from '@/types'

// Define the states outside the function to make all components share the same state instead of creating a new state for each component
const bookings = ref<Booking[]>([])
const bookingsLoading = ref(false)

export function useBookings() {

  // just move all the logic related to bookings here
  const fetchBookings = async () => { ... }
  const bookEvent = async (event: Event) => { ... }
  const cancelBooking = async (bookingId: string) => { ...}
  onMounted(() => fetchBookings())

  // return a object (this is convention of composables) to expose the states and methods (include the global states)
  return {
    bookings,
    bookingsLoading,
    fetchBookings,
    bookEvent,
    cancelBooking
  }
}
```

The point is to place the shared state outside the function to make all components share the same state instead of creating a new state for each component. Then, we can use this composable in any component that needs to access the bookings state or methods. It can simplify the logic of sharing states between components greatly.

```vue
<!-- BookingList.vue -->
<template> ... </template>

<script setup lang="ts">
...
import { useBookings } from '@/composables/useBookings'

const { bookings, bookingsLoading, cancelBooking } = useBookings()  // only import the composable and take what you need in the component
</script>
```

```vue
<!-- EventList.vue -->
<template>
...
          <EventCard v-for="event in events" :key="event.id" :title="event.title" :when="event.date"
          :description="event.description" @register="bookEvent(event)" />
          <!--                                        ^^^^^^^^^^^^^^^^ instead of emitting the event to App.vue, now we can directly call the bookEvent method from the composable -->
...
</template>

<script setup lang="ts">
...

import { useBookings } from '@/composables/useBookings';

const { bookEvent } = useBookings()  // only import the composable and take what you need in the component

...
</script>
```

As shown above, with the introduction of composable, we can directly use the same composable in different components, extract the required states and methods, so there is no need to pass states through props and emit anymore, saving a lot of code and simplifying logic. It is less prone to bugs and easier to maintain.


