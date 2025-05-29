---
date: 2025-05-16
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Augment Pinia Type

If you use a Pinia plugin to add a custom property to the store as below:

```ts
...
const pinia = createPinia()

function ourPlugin() {
    return {
        secret: 'Dummy'
    }
}

pinia.use(ourPlugin)
...

```

Even if you can really use `store.secret` to get the value of 'Dummy', there will be a Typescript error in the IDE, so we need to use `*.d.ts` to extend Pinia's type definitions.

## 1. Create a `*.d.ts` file

```sh
 tree src
src
├── App.vue
├── assets
├── components
├── main.ts
├── plugins
├── router
├── stores
├── types
│   └── pinia.d.ts # Create a file with extension of d.ts, which means TypeScript definition file
└── views
```

## 2. Add the following code to `pinia.d.ts`

```ts
import 'pinia'; // it's important to import pinia first, or below definition will replace the original one

// The following codes are used to extend the type definitions of pinia
declare module 'pinia' {
  export interface PiniaCustomProperties {
  //               ^^^^^^^^^^^^^^^^^^^^^ we want to extend this interface, so the name must be the same
    secret: string; // extend the type of secret to the interface `PiniaCustomProperties`
  }
}
```

## 3. Modify the `tsconfig.json` file

```json
{
  ...
  "include": [
    "env.d.ts",
    "src/**/*",
    "src/**/*.vue",
    "src/**/*.d.ts" <------------ add this line
  ],
  ...
}
```

