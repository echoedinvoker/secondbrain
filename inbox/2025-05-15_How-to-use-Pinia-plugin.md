---
date: 2025-05-15
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# How to use Pinia plugin

## Pinia plugin

Pinia plugin is just a function.
```js
function piniaPlugin() {
  return {
    secret: 'Dummy', // 'secret' will become a custom property of all stores
                     // which means you can access it in any store
  }
}
```
## Use Pinia plugin
```js
const app = createApp(App)
const pinia = createPinia()

pinia.use(piniaPlugin) // use the plugin
app.use(pinia) // use pinia
```
