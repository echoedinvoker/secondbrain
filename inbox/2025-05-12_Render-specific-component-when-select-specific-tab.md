---
date: 2025-05-12
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Render specific component when select specific tab

This is a common use case of dynamic component `<component>` in Vue.js.

In [[2025-05-12_Tab-UI|Tab UI]], we already have a tab system. Now we want to render a specific component when the user selects a specific tab.

So the first step is to create components for each tab:

```sh
$ tree src
src
├── App.vue
├── assets
├── components
│   ├── GeneralSettings.vue # new component for tab key `General`
│   ├── NotificationsSettings.vue # new component for tab key `Notifications`
│   ├── PrivacySettings.vue # new component for tab key `Privacy`
│   └── TabLink.vue
├── main.ts
└── types.ts
```
We can directly add a new property to `Tab` type pointing to the component that belongs to the tab:

```ts
// src/types.ts
import type { Component } from "vue"; // Vue provides a type `Component` for components

export type TabKey = 'General' | 'Notifications' | 'Privacy';

export interface Tab {
  key: TabKey;
  label: string;
  component: Component; // add new property `component` to the type `Tab`
}
```

Then we modify the data `tabs` in `App.vue` to include the new property `component`:

```vue
<!-- src/App.vue -->
<script setup lang="ts">
// ...

const tabs: Tab[] = [
  {
    key: 'General',
    label: 'General',
    component: GeneralSettings, // add property `component` and its associated component
  },
  {
    key: 'Notifications',
    label: 'Notifications',
    component: NotificationsSettings,  // add property `component` and its associated component
  },
  {
    key: 'Privacy',
    label: 'Privacy',
    component: PrivacySettings,  // add property `component` and its associated component
  }
]

const currentTab = ref<TabKey>('General');

// derive the current tab component from the current tab key value
const currentTabComponent = computed(() => tabs.find(tab => tab.key === currentTab.value)?.component);

</script>

<template>
  <main class="max-w-2xl mx-auto px-4">
    <nav class="font-medium text-center text-gray-500 border-b border-gray-200 mb-4">
      <ul class="flex flex-wrap -mb-px">
        <li v-for="tab in tabs" :key="tab.key">
          <TabLink
            :currentTab="currentTab"
            :tab="tab"
            @click="currentTab = tab.key"
          />
        </li>
      </ul>
    </nav>
    <component :is="currentTabComponent" />  <!-- render the current tab component -->
  </main>
</template>
```
