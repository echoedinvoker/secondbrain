---
date: 2025-05-06
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Do not put vif and vfor on the same element and template element

```vue
<template>
  <div v-for="item in items" :key="item.id" v-if="item.isVisible">
    {{ item.name }}
  </div>
  ...
</template>
```

Above code is not recommended because `v-for` and `v-if` are not designed to work together on the same element. This can lead to unexpected behavior and performance issues.

We can use a wrapper element to separate the two directives:

```vue
<template>
  <div v-if="item.isVisible"> <!-- Wrapper element -->
    <div v-for="item in items" :key="item.id">
      {{ item.name }}
    </div>
  </div>
  ...
</template>
```

But if there is a grid or list container, it may break the layout.

```vue
<template>
  <div class="grid grid-cols-3 gap-4">
      <div v-if="item.isVisible">
        <div v-for="item in items" :key="item.id">
          {{ item.name }}
        </div>
      </div>
  </div>
  ...
</template>
```

So, if you want to add a wrapper element but do not want to break the layout, you can use `template` element, which is a special element in Vue that does not render to the DOM.

```vue
<template>
  <template v-if="item.isVisible">
    <div v-for="item in items" :key="item.id">
      {{ item.name }}
    </div>
  </template>
  ...
```

`template` element is useful when you want rendering elements conditionally without adding extra elements to the DOM.

