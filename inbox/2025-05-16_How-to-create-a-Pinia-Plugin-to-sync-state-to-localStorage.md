---
date: 2025-05-16
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# How to create a Pinia Plugin to sync state to localStorage

```ts
// src/plugins/pinia/localStoragePlugin.ts (create a new file for the plugin)
import type { PiniaPluginContext } from 'pinia';

// pinia plugin is just a function
const localStoragePlugin = (context: PiniaPluginContext) => {
//                          ^^^^^^^^^^^^^^^^^^^^^^^^^^^ pinia passes an argument to the plugin,
//                                                      we can use it to access the store

  const { store } = context; // get the store from the context


  // ####### initialize the store with the data from localStorage if it exists #######

  const storedState = localStorage.getItem(store.$id); // 'store.$id' is the name of the store, such as 'user'

  // initialize the store with the data from localStorage if it exists
  if (storedState) {
    store.$patch(JSON.parse(storedState)); // we use 'store.$patch' to update the store state,
                                           // and remember to parse the JSON string because localStorage only stores strings
  }


  // ####### modify the data in localStorage when the store changes by actions #######

  store.$subscribe((mutation, state) => { // we use 'store.$subscribe' to listen to the store changes
  //                ^^^^^^^^ mutation is the action that was called, but we don't need it here because we simply update the data of the localStorage
  //                         when the store changes anyway
    localStorage.setItem(store.$id, JSON.stringify(state)); // we use 'localStorage.setItem' to update the data in localStorage
    //                                                      // and key is the name of the store 'store.$id',
    //                                                      // value is the state of the store (2nd param), remember to stringify it
  });
};

export default localStoragePlugin;
```

Then, we can use it in the main.ts by `pinia.use`:

```ts
import './assets/main.css';

import { createApp } from 'vue';
import { createPinia } from 'pinia';

import App from '@/App.vue';
import localStoragePlugin from '@/plugins/pinia/localStoragePlugin'; // import the plugin

const app = createApp(App);
const pinia = createPinia(); // create a pinia instance

pinia.use(localStoragePlugin); // use the plugin
app.use(pinia);

app.mount('#app');
```
