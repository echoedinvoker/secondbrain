---
date: 2025-05-07
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Optimistic UI on adding item

```vue
<template>
  ...
        <EventCard v-for="event in events" :key="event.id" :title="event.title" :when="event.date"
          :description="event.description" @register="bookEvent(event)" />
  ...
        <BookingItem v-for="book in bookings" :key="book.id" :title="book.eventTitle" :user="book.userId"
        :status="book.status" />
  ...
</template>

<script setup lang="ts">

...

interface Booking {
  id: string
  userId: number
  eventId: number
  eventTitle: string
  status: string
}
const bookings = ref<Booking[]>([])

...

const bookEvent = async (event: Event) => {
  const newBooking = {
    id: Date.now().toString(),
    userId: 1,
    eventId: event.id,
    eventTitle: event.title,
  }
  await fetch('http://localhost:3001/bookings', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      ...newBooking,
      status: 'confirmed',
    })
  })
}

...
</script>
```

In the above codes, when we click on the register of EventCard, it will send a POST request to the bookings API. We have to wait for this request to complete before refreshing the page to see the new booking. This user experience is very poor. We can add the booking to bookings before sending the request, so that users can see the new booking immediately after clicking register. This practice is called Optimistic UI.

```js
const bookEvent = async (event: Event) => {
  const newBooking = {
    id: Date.now().toString(),
    userId: 1,
    eventId: event.id,
    eventTitle: event.title,
    status: 'pending'  // add status `pending` to represent that the booking is not confirmed yet
  }

  bookings.value.push(newBooking) // before sending the request, add the booking to bookings
                                  // so that users can see the new booking immediately

  await fetch('http://localhost:3001/bookings', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      ...newBooking,
      status: 'confirmed',
    })
  })
}
```

Then we need to update the status of the booking based on the response. If the request is successful, change the booking status to confirmed. If the request fails, remove the booking or change its status to failed.

```js
const bookEvent = async (event: Event) => {

  // ...

  // wrap the fetch with try-catch for unexpected errors
  try {
    const response = await fetch('http://localhost:3001/bookings', { ... })

    // check if the response is ok, if not, throw an error to be caught by the catch block
    if (!response.ok) throw new Error('Failed to book event')

    // if the response is ok, update the booking status to confirmed
    const booking = await response.json() // assuming the server returns the booking object
    const index = bookings.value.findIndex((b) => b.id === newBooking.id)
    bookings.value[index] = booking

  // centralized error handling here
  } catch (error) {
    console.error('Error booking event:', error)
    // if the request fails, remove the booking
    bookings.value = bookings.value.filter((booking) => booking.id !== newBooking.id)
    // or change its status to failed
    // const index = bookings.value.findIndex((b) => b.id === newBooking.id)
    // bookings.value[index].status = 'failed'
  }
}
```

So basically, there are two key points in implementing Optimistic UI:
1. Update the UI before sending the request by adding the new data while waiting for the request to complete
2. Synchronize the UI with the server response after the request is completed by updating the data based on the response.
