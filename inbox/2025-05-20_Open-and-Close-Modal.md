---
date: 2025-05-20
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Open and Close Modal

To open and close a modal of [[2025-05-20_Modal-Dialog-Style|Modal Dialog Style]], we can set a boolean ref in the component which uses the modal and use it to control the modal rendering or not

```vue
<!--src/App.vue-->
<script setup lang="ts">

//...

import ModalDialog from './components/ModalDialog.vue';

// ...

const isModalOpen = ref(false) // boolean ref to control modal rendering

// actions to open and close the modal
const openModal = () => { isModalOpen.value = true }
const closeModal = () => { isModalOpen.value = false }
</script>

<template>
  <main class="p-5 font-sans">
    <div class="flex gap-5 py-5 overflow-x-auto">
      ...
      <button
        @click="openModal" <!-- call the openModal action to open the modal -->
        class="w-full bg-transparent rounded hover:bg-white text-gray-500 p-2 text-left mt-2 text-sm font-medium">
        + Add Card
      </button>
    </div>

    <ModalDialog :is-open="isModalOpen" />  <!-- modal component and props isModalOpen to control rendering -->
  </main>
</template>
```

Then, we should set the `isOpen` prop in the modal component to control the rendering of the modal.

```vue
<!--src/components/ModalDialog.vue-->
<script lang="ts" setup>
defineProps<{ isOpen: boolean }>() // define the prop isOpen
const emit = defineEmits<{
  (e: 'close'): void
}>()
</script>

<template>
  <div
    v-if="isOpen" <!-- use v-if to control rendering -->
    class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center" role="dialog" aria-modal="true"
  >
    <div class="bg-white p-5 rounded max-w-md w-full"> ... </div>
  </div>
</template>
```

Above, we complete the logic to open the modal, then we can add the logic to close the modal.

When user clicks any buttons in the modal or press ESC key when the modal is open, we should close the modal.

```vue
<!--src/components/ModalDialog.vue-->
<script lang="ts" setup>
defineProps<{ isOpen: boolean }>()

// define the emit 'close' event
const emit = defineEmits<{
  close: []
}>()
</script>

<template>
  <div
    v-if="isOpen"
    @keydown.esc="emit('close')" <!-- when user press ESC key, emit the close event -->
    class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center" role="dialog" aria-modal="true"
  >
    <div class="bg-white p-5 rounded max-w-md w-full">
      
      ...

      <div class="flex justify-end gap-2">
        <button
          @click="emit('close')" <!-- when user clicks the cancel button, emit the close event -->
          class="bg-gray-300 hover:bg-gray-200 text-black px-4 py-2 rounded"
        >Cancel</button>
        <button
          @click="emit('close')" <!-- when user clicks the save button, emit the close event -->
          class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded"
        >Save</button>
      </div>
    </div>
  </div>
</template>
```

Then, we can listen to the close event in the parent component and call the closeModal action to close the modal.

```vue
<!--src/App.vue-->
<script setup lang="ts">

// ...

const isModalOpen = ref(false)
const openModal = () => { isModalOpen.value = true }
const closeModal = () => { isModalOpen.value = false } // close the modal
</script>

<template>
  <main class="p-5 font-sans">
    <div class="flex gap-5 py-5 overflow-x-auto"> ... </div>

    <ModalDialog
      :is-open="isModalOpen"
      @close="closeModal" <!-- listen to the close event and call the closeModal action -->
    />
  </main>
</template>
```

Now, we can open and close the modal by clicking the buttons in the modal or pressing the ESC key when the modal is open.

But there is a problem that when the modal is open, the user must activate one of the inputs in the modal to close the modal by pressing the ESC key.
