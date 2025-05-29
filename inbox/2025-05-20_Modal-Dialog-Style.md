---
date: 2025-05-20
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Modal Dialog Style

```vue
<script lang="ts" setup>
</script>

<template>
  <!-- Modal Overlay and Positioning content in the center -->
  <div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center" role="dialog" aria-modal="true">

    <!-- Modal Content -->
    <div class="bg-white p-5 rounded max-w-md w-full">
      <h2 class="text-xl font-bold mb-4">Add New Card</h2>
      <input type="text" placeholder="Card Title" aria-label="Card Title" class="w-full p-2 mb-4 border rounded" />
      <textarea class="w-full p-2 mb-4 border rounded" placeholder="Description" aria-label="Card Description"></textarea>

      <div class="flex justify-end gap-2">
        <button class="bg-gray-300 hover:bg-gray-200 text-black px-4 py-2 rounded">Cancel</button>
        <button class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded">Save</button>
      </div>
    </div>

  </div>
</template>
```

- `fixed inset-0` is a class combination in Tailwind CSS used to fix an element to all four edges of the window and make it completely fill the entire window. This is typically used to create full-screen modal dialogs or overlay layers.

- `bg-black bg-opacity-50` sets the background color to black with 50% opacity, creating a semi-transparent overlay effect.

- `flex items-center justify-center` centers the modal content both vertically and horizontally within the viewport.
