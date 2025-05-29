---
date: 2025-05-20
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Trap focus in the modal

In [[2025-05-20_Solution-to-ESC-close-modal-issue-(auto-focus-on-input-field)|Solution to ESC close modal issue (auto focus on input field)]], we mention that the user can still tab out of the modal, so we need to trap the focus inside the modal.

We can install the `@vueuse/core` and `@vueuse/integrations` packages to use the `useFocusTrap` composable, which will help us trap the focus inside the modal.


```vue
<!--src/components/ModalDialog.vue-->
<script lang="ts" setup>

// ...

const props = defineProps<{
  isOpen: boolean
}>()

// ...

const modalElement = ref<HTMLDivElement | null>(null) // create a ref for the modal element
const { activate, deactivate } = useFocusTrap(modalElement) // pass the modal element to the useFocusTrap composable to get the activate and deactivate functions

// Replace listening to individual refs with listening to isOpen.
watch(() => props.isOpen, async (isOpen) => {
  if (isOpen) {
    await nextTick() // Wait for the DOM to update
    activate() // Activate the focus trap
    titleInput.value?.focus()
  } else {
    deactivate() // Deactivate the focus trap when isOpen is false, this is better than using `unmounted` lifecycle hook
  }
})
</script>

<template>
  <div

    <!-- ... -->

    ref="modalElement" <!-- bind the ref to the modal element, where the focus trap will be applied -->
  >

    <!-- Modal content... -->

  </div>
</template>
```

