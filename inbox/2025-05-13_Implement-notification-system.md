---
date: 2025-05-13
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Implement notification system

We will use the concept of the [[2025-05-13_Design-diagram-of-presenting-notifications|Design diagram of presenting notifications]] to implement the notification system in code.

## Composable function to handle shared state
```ts
// useNotification.ts
import { ref } from 'vue';

interface Notification {
  id: number;
  message: string;
}

const notifications = ref<Notification[]>([]); // shared state

const addNotification = (message: string) => {
  const id = Date.now(); // if only one person is using the app, this will be unique (in our case, it always is)
  notifications.value.push({
    id,
    message
  });

  setTimeout(() => removeNotification(id), 5000); // automatically remove after 5 seconds
};

// beside automatically removing the notification, we can also provide a way to remove it manually
const removeNotification = (id: number) => {
  notifications.value = notifications.value.filter((notification) => notification.id !== id);
};

// export with composable function
export function useNotifications() {
  return {
    notifications,
    addNotification,
    removeNotification
  };
}
```

## Component to display notifications

```vue
<!--NotificationList.vue-->
<template>
  <Teleport to="body"> <!-- teleport children to body, so it can be displayed with absolute position according to the body -->
    <ol class="notification-list">

      <!-- render all notifications iteratively -->
      <li v-for="notification in notifications" :key="notification.id" class="notification-item">
        <div class="text-sm font-semibold">
          {{ notification.message }}
        </div>

        <!-- we can use a button to remove the notification manually -->
        <button @click="removeNotification(notification.id)" class="notification-ack-button">
          OK
        </button>

      </li>
    </ol>
  </Teleport>
</template>

<script setup lang="ts">
import { useNotifications } from '@/composables/useNotifications';

const { notifications, removeNotification } = useNotifications(); // get the shared state and functions from the composable we just created
</script>
```

Some classes `notification-list` and `notification-item` above are customized using @apply, but the content is copied from Shadcn UI's NotificationList. This is a convenient way, but I won't explain it in detail here because the focus is not on styling.

Because the markup of this component will be teleported to the body no matter what, using it anywhere will not affect the display of this component, usually placed inside the main layout.


## Binding method `addNotification` to the button

We can use the `addNotification` method anywhere we want to trigger a notification, here we use a form submit button as an example.

```vue
<script setup lang="ts">
import { useNotifications } from '@/composables/useNotifications';
import { useSettings } from '@/composables/useSettings';

const { general } = useSettings();
const { addNotification } = useNotifications(); // get the method to add a notification from composable
</script>

<template>
  <div>
    <h2 class="text-2xl mb-4">General</h2>
    <form
      class="space-y-4 mx-auto"
      @submit.prevent="() => addNotification('Settings saved successfully!')" <!-- call the method to add a notification when the form is submitted -->
    >
      ...
      <button type="submit" class="btn-primary">Save</button>
    </form>
  </div>
</template>
```

Now, when the form is submitted, a notification will be displayed at the right-bottom of the screen. The notification will automatically disappear after 5 seconds, but can also be removed manually by clicking the "OK" button.
