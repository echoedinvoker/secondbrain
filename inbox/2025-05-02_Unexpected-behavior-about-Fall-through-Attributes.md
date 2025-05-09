---
date: 2025-05-02
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Unexpected behavior about Fall through Attributes

```vue
<!--Parent.vue-->
<template>
  <Button label="Register" @click="$emit('register')" />
</template>

<script setup lang="ts">
import Button from '@/components/Button.vue';

defineProps({
  title: String,
})

defineEmits(['register'])
</script>
```
```vue
<!--Button.vue-->
<template>
  <button @click="$emit('click')">{{ label }}</button>
</template>

<script setup lang="ts">
defineProps({
  label: String,
})
</script>
```

Assuming you want to trigger the 'register' event in Parent.vue from the click event of Button.vue, in reality this will emit the 'register' event twice.

Because `@click="$emit('register')"` will fall through to the root element of `Button.vue`, this will cause two listeners to actually listen to the same event on the button.

If we have a clear definition of the 'click' event in Button.vue, we can actually avoid the above problem from happening.

```vue
<!--Button.vue-->
<template>
  <button @click="$emit('click')">{{ label }}</button>
</template>

<script setup lang="ts">
defineProps({
  label: String,
})

defineEmits(['click']) // Define the click event of Button.vue
</script>
```

Now the `click` event of Button.vue will not fall through to the root element because it is already defined in the component. This will prevent the 'register' event from being emitted twice.


