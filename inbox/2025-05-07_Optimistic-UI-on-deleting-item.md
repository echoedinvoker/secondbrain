---
date: 2025-05-07
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Optimistic UI on deleting item

```vue
<template>
  ...
        <BookingItem v-for="book in bookings" :key="book.id" :title="book.eventTitle" :user="book.userId"
        :status="book.status"
        @cancel-booking="cancelBooking(book.id)"
        />
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

const cancelBooking = async (bookingId: string) => {
  await fetch(`http://localhost:3001/bookings/${bookingId}`, {
    method: 'DELETE'
  })
}

...
</script>
```

After clicking the cancel-booking button, we need to refresh the page to see that the data has been deleted. This is very unfriendly for users. We can use optimistic UI to improve this issue.

The practice of Optimistic UI is to update the interface before sending the request. In other words, first delete the data to be deleted from the state `bookings`, then send the request. If the request fails, add the data back.

```js
const cancelBooking = async (bookingId: string) => {
  // find the index of the booking to be deleted for splicing
  const index = bookings.value.findIndex((booking) => booking.id === bookingId)
  // store the original booking to a variable, so we can add it back if the request fails
  const originalBooking = bookings.value[index]
  // optimistic update: remove the booking from the state
  bookings.value.splice(index, 1)

  // wrap the request in a try-catch block for unexpected errors
  try {
    const response = await fetch(`http://localhost:3001/bookings/${bookingId}`, {
      method: 'DELETE'
    })
    // if anything goes wrong, throw an error to the catch block
    if (!response.ok) throw new Error('Failed to cancel booking')

  // centralized error handling
  } catch (error) {
    console.error('Error canceling booking:', error)
    bookings.value.splice(index, 0, originalBooking) // add the booking back to the state if the request fails
  }
}
```


