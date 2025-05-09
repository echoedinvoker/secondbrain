---
date: 2025-05-02
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Fall through Attributes

The fall-through attributes are the attributes that are passed from the parent component to the child component. This is useful when you want to pass down attributes like `class`, `style`, or event listeners to a child component without explicitly defining them in the child component.

```vue
<!--Parent.vue-->
<template>
  <Button label="Register" class="text-red-500" @click="$emit('register', title)" />
                       <!--^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^-->
                       <!--if we add class and v-on on the custom component,-->
                       <!--they will be fall through to the root element of the component-->
</template>

<script setup lang="ts">
import Button from '@/components/Button.vue';

defineProps({
  title: String, // even `@click="$emit('register', title)"` will be fall through the Button component
                 // the values of parent component still works
})

defineEmits(['register'])
</script>
```
```vue
<!--Button.vue-->
<template>
  <button class="text-sm font-medium border border-gray-200 rounded-full px-3 py-1 hover:bg-gray-100">{{ label }}</button>
      <!--^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^-->
      <!--even there is not any utility class of text color, but the class `text-red-500` will be fall through from the parent-->
      <!--and the onClick event will be fall through to the button element also-->
</template>

<script setup lang="ts">
defineProps({
  label: String,
})
</script>
```

But sometimes, if you forgot this feature, you may end up with some unexpected behavior. 
[[2025-05-02_Unexpected-behavior-about-Fall-through-Attributes|Unexpected behavior about Fall through Attributes]]
