---
date: 2025-05-20
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Exit Modal by Clicking Overlay

Assume we control the display of a modal dialog in its parent component. We want to close the modal when the user clicks outside of it, on the overlay.

```vue
<!--src/components/ModalDialog.vue-->
<template>
  <div

    <!-- Modal Overlay defined in this markup -->

    @click.self="emit('close')" <!-- Close modal when clicking on overlay -->
    <!--  ^^^^^ use .self modifier to ensure only the overlay click closes the modal, not any child elements -->
  >

    <!-- modal content -->

  </div>
</template>
```

