---
date: 2024-10-04
type: fact
aliases:
  -
hubs:
  - "[[Vue]]"
---

# Start a easiest Tiptap project on Vue3

## Setup project

```bash
# create a project
vue create my-tiptap-project

# change directory
cd my-tiptap-project

# install dependencies
npm install @tiptap/vue-3 @tiptap/pm @tiptap/starter-kit
```

## Easy Tiptap

src/components/MyTiptap.vue
```vue
<template>
  <editor-content :editor="editor" />
</template>

<script setup>
import { useEditor, EditorContent } from '@tiptap/vue-3'
import StarterKit from '@tiptap/starter-kit'

const editor = useEditor({
  content: '<p>I’m running Tiptap with Vue.js. 🎉</p>',
  extensions: [StarterKit],
})
</script>
```


## Import Tiptap into App.vue and use it

src/App.vue
```vue
<template>
  <div id="app">
    <my-tiptap />
  </div>
</template>

<script>
import MyTiptap from './components/MyTiptap.vue'

export default {
  name: 'App',
  components: {
    MyTiptap,
  },
}
</script>
```
