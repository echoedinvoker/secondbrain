---
date: 2025-05-20
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Solution to ESC close modal issue (auto focus on input field)

In [[2025-05-20_Open-and-Close-Modal|Open and Close Modal]], we mentioned that the modal can be closed by pressing the ESC key. However, this feature only works when one of the input fields is focused.

One solution is to automatically focus on the first input field when the modal appears.


```vue
<!--src/components/ModalDialog.vue-->
<script lang="ts" setup>

// ...

// create a ref to hold the input element
const titleInput = ref<HTMLInputElement | null>(null)

// when titleInput loads, focus on it
watch(titleInput, (value) => {
  if (value) {
    value.focus()
  }
})
</script>

<template>

  <!-- ... -->

      <input
        <!-- ... -->
        ref="titleInput" <!-- binding the ref to this input element -->
      />

      <!-- ... -->

    </div>
  </div>
</template>
```
But this solution is not ideal. If user clicks anywhere out of the input fields, the ESC key will not work.


## Better solution (from myself)

I personally think that a better way is to directly bind the keydown event to the document, so that it can avoid the problem of not being able to trigger ESC after the focus disappears due to user clicks.

We should implement this logic in the parent component of the modal dialog, which is the `App.vue` file in this case.

```vue
<!--src/App.vue-->
<script setup lang="ts">

// ...

const isModalOpen = ref(false)

const openModal = () => {
  isModalOpen.value = true
}

const closeModal = () => {
  isModalOpen.value = false
}

// callback function of onMounted and onUnmounted should be the same, so we MUST define it outside
const handleEscapeKey = (event: KeyboardEvent) => {
  if (event.key === 'Escape' && isModalOpen.value) {
    closeModal()
  }
}

// add event listener to document when modal is open
onMounted(() => {
  document.addEventListener('keydown', handleEscapeKey)
})

// remove event listener from document when modal is closed, or it will cause memory leak
onUnmounted(() => {
  document.removeEventListener('keydown', handleEscapeKey)
})
</script>

<template>
  <main class="p-5 font-sans">
    <div class="flex gap-5 py-5 overflow-x-auto"> ... </div>

    <!-- ESC key will work even when the input field is not focused, so it's better solution -->
    <ModalDialog :is-open="isModalOpen" @close="closeModal" />

  </main>
</template>
```
