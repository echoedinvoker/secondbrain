
```vue
<!--src/components/dataSource/QueryEditor.vue-->
<template>
  <editor-content :editor="editor" />
</template>

<script setup lang="ts">
import { useEditor, EditorContent } from "@tiptap/vue-3";
import Document from "@tiptap/extension-document";
import Paragraph from "@tiptap/extension-paragraph";
import Text from "@tiptap/extension-text";
import HardBreak from "@tiptap/extension-hard-break";
import Mention from "@tiptap/extension-mention";
import suggestion from "@/helpers/sugguestions";
import { onMounted } from "vue";

const props = defineProps(["value"]);
const emit = defineEmits(["update"]);

const CustomDocument = Document.extend({
  content: "block+",
});

const CustomParagraph = Paragraph.extend({
  content: "inline*",
});

const editor = useEditor({
  content: props.value,
  extensions: [
    CustomDocument,
    CustomParagraph,
    Text,
    HardBreak.extend({
      addKeyboardShortcuts() {
        return {
          Enter: () => this.editor.commands.setHardBreak(),
        };
      },
    }),
    Mention.configure({
      HTMLAttributes: {
        class: "mention",
      },
      suggestion: {
        ...suggestion,
        char: "@P(",
      },
    }),
  ],
  onUpdate({ editor }) {
    let content = editor.getHTML();
    content = htmlToText(content)
    emit("update", content);
  },
});

function decodeHTMLEntities(text: string) {
  const textarea = document.createElement("textarea");
  textarea.innerHTML = text;
  return textarea.value;
}

function htmlToText(html: string): string {
  let text = html.replace(/<br\s*\/?>/gi, '\n')
    .replace(/<p\s*\/?>/gi, '\n')
    .replace(/<\/p>/gi, '\n');
  text = text.replace(/<[^>]+>/g, '');
  text = decodeHTMLEntities(text);
  text = text.replace(/\n{3,}/g, '\n');
  text = text.replace(/\n{2,}/g, '\n');
  return text.trim();
}

onMounted(() => {
  if (editor.value && props.value) {
    let escapedContent = props.value
      .replace(/\n\n/g, "<br>")
      .replace(/\n/g, "<br>")
      .replace(/\t/g, "&emsp;")
      .replace(/ /g, "&nbsp;");

    // 處理 @P( 前方相鄰HTML entity 導致功能失效問題
    escapedContent = escapedContent.replace(/(&emsp;|&nbsp;| )(@P\()?/g, (match: string, space: string, mention: string) => {
      if (mention) {
        // 如果后面跟着 @P(，就用普通空格
        if (space === '&emsp;') return '  ' + mention;
        if (space === '&nbsp;' || space === ' ') return ' ' + mention;
      } else {
        // 如果后面没有 @P(，就用 &nbsp;
        if (space === '&emsp;') return '&emsp;';
        return '&nbsp;';
      }
      return match; // 这行实际上不会被执行到，但为了完整性保留
    });

    editor.value.commands.setContent(escapedContent);
  }
});
</script>
```

```vue
<!-- src/components/dataSource/MentionList.vue  -->
<template>
  <div class="dropdown-menu">
    <div v-if="isLoading">Loading...</div>
    <template v-else-if="suggestions.length">
      <button :class="{ 'is-selected': index === selectedIndex }" v-for="(item, index) in suggestions" :key="index"
        @click="selectItem(index)">
        {{ item }}
      </button>
    </template>
    <div class="item" v-else>
      No result
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue'
import { useSuggestionStore } from '@/stores/suggestions/suggestionsStore'
import { storeToRefs } from 'pinia';
import { useSuggestions } from '@/hooks/suggestions/useSuggestions'

const store = useSuggestionStore()
const { query, currentQueryString, currentCategory, lastPipeLocation } = storeToRefs(store)

const { suggestions, isLoading } = useSuggestions()

const props = defineProps({
  command: {
    type: Function,
    required: true,
  },
})

const selectedIndex = ref(0)

watch(() => suggestions.value, () => {
  selectedIndex.value = 0
})

function onKeyDown({ event }: { event: KeyboardEvent }) {
  if (event.key === 'ArrowUp') {
    upHandler()
    return true
  }
  if (event.key === 'ArrowDown') {
    downHandler()
    return true
  }
  if (event.key === 'Enter') {
    enterHandler()
    return true
  }
  return false
}

function upHandler() {
  selectedIndex.value = ((selectedIndex.value + suggestions.value.length) - 1) % suggestions.value.length
}

function downHandler() {
  selectedIndex.value = (selectedIndex.value + 1) % suggestions.value.length
}

function enterHandler() {
  selectItem(selectedIndex.value)
}

function selectItem(index: number) {
  if (isLoading.value) return
  const item = suggestions.value[index]
  if (item) {
    props.command({ id: item })
  }
}

defineExpose({ onKeyDown })
</script>
```

```ts
// src/hooks/suggestions/useSuggestions.ts 
import { useQuery } from "@tanstack/vue-query";
import { useSuggestionStore } from "@/stores/suggestions/suggestionsStore"
import { computed } from "vue";
import { getSysParamsTypeByName, getSysParamsTypeByParentTypeId } from "@/services/apiSysParamsType";
import { getSysParamsbyCategoryId } from "@/services/apiParamsTable";

export function useSuggestions() {
  const store = useSuggestionStore()
  const currentCategory = computed(() => store.currentCategory)
  const currentQueryString = computed(() => store.currentQueryString)

  const { data, isLoading } = useQuery({
    queryKey: [
      currentCategory,
      currentQueryString
    ],
    queryFn: async () => {
      if (!currentCategory.value) {
        const topLevelTypes = await getSysParamsTypeByParentTypeId(0)

        store.isParameterType = true
        const categoryNames = topLevelTypes.map(type => type.category_name)
        return categoryNames.filter(type => {
          return type.includes(currentQueryString.value ?? "")
        })
      } else {
        const categoryId = (await getSysParamsTypeByName(currentCategory.value)).id
        const parameterTypes = await getSysParamsTypeByParentTypeId(categoryId)
        if (parameterTypes.length) {
          store.isParameterType = true
          const categoryNames = parameterTypes.map(type => type.category_name)
          return categoryNames.filter(type => {
            return type.includes(currentQueryString.value ?? "")
          })
        }
        store.isParameterType = false
        const parameterIds = (await getSysParamsbyCategoryId(categoryId)).map(param => param.parameter_id)
        return parameterIds.filter(param => {
          return param.includes(currentQueryString.value ?? "")
        })
      }
    },
  })

  return {
    suggestions: data,
    isLoading
  }
}
```

```ts
// src/helpers/sugguestions.ts 
import { VueRenderer } from '@tiptap/vue-3'
import tippy from 'tippy.js'

import MentionList from '@/components/dataSource/MentionList.vue'

import { useSuggestionStore } from '@/stores/suggestions/suggestionsStore'

export default {
  items: ({ query }: { query: string }) => {
    const store = useSuggestionStore()
    store.updateQuery(query)
  },

  command: ({ editor, range, props }: { editor: any, range: any, props: any }) => {
    const item = props.id;

    if (!item) {
      console.error('No item provided');
      return;
    }

    const store = useSuggestionStore();

    const offset = store.query.at(-1) === '|' ? 0 : store.query.split('|').at(-1)?.length ?? 0;

    if (store.isParameterType) {
      editor
        .chain()
        .focus()
        .insertContentAt({ from: range.to - offset, to: range.to }, item + '|')
        .run();

      return false;
    } else {
      editor
        .chain()
        .focus()
        .insertContentAt({ from: range.to - offset, to: range.to }, item + ') ')
        .run();

      return true;
    }
  },

  render: () => {
    let component: VueRenderer
    let popup: any

    return {
      onStart: (props: any) => {
        component = new VueRenderer(MentionList, {
          props,
          editor: props.editor,
        })

        if (!props.clientRect) {
          return
        }

        popup = tippy('body', {
          getReferenceClientRect: props.clientRect,
          appendTo: () => document.body,
          content: component.element,
          showOnCreate: true,
          interactive: true,
          trigger: 'manual',
          placement: 'bottom-start',
        })
      },

      onUpdate(props: any) {
        component.updateProps(props)

        if (!props.clientRect) {
          return
        }

        popup[0].setProps({
          getReferenceClientRect: props.clientRect,
        })
      },

      onKeyDown(props: any) {
        if (props.event.key === 'Escape') {
          popup[0].hide()

          return true
        }

        return component.ref?.onKeyDown(props)
      },

      onExit() {
        popup[0].destroy()
        component.destroy()
      },


    }
  },
}
```
請問為什麼我在輸入一些文字用來過濾mentionlist中出現的選項時, 如果選項中有第二個英文字母是大寫的項目我就無法過濾出來

🤖:[ChatClaude-3-5-Sonnet]
這個問題可能是由於過濾邏輯對大小寫敏感造成的。讓我們仔細檢查一下代碼，特別是在 `useSuggestions.ts` 文件中的過濾邏輯。

在 `useSuggestions.ts` 中，我們可以看到兩個地方使用了 `includes` 方法來過濾結果：

```typescript
return categoryNames.filter(type => {
  return type.includes(currentQueryString.value ?? "")
})
```

和

```typescript
return parameterIds.filter(param => {
  return param.includes(currentQueryString.value ?? "")
})
```

`includes` 方法是大小寫敏感的，這意味著 "ab" 不會匹配 "Ab"。為了解決這個問題，我們可以在比較時將兩邊都轉換為小寫。

以下是修改後的 `useSuggestions.ts` 文件中的相關部分：

```typescript
export function useSuggestions() {
  // ... 前面的代碼保持不變

  const { data, isLoading } = useQuery({
    queryKey: [
      currentCategory,
      currentQueryString
    ],
    queryFn: async () => {
      if (!currentCategory.value) {
        const topLevelTypes = await getSysParamsTypeByParentTypeId(0)

        store.isParameterType = true
        const categoryNames = topLevelTypes.map(type => type.category_name)
        return categoryNames.filter(type => {
          return type.toLowerCase().includes((currentQueryString.value ?? "").toLowerCase())
        })
      } else {
        const categoryId = (await getSysParamsTypeByName(currentCategory.value)).id
        const parameterTypes = await getSysParamsTypeByParentTypeId(categoryId)
        if (parameterTypes.length) {
          store.isParameterType = true
          const categoryNames = parameterTypes.map(type => type.category_name)
          return categoryNames.filter(type => {
            return type.toLowerCase().includes((currentQueryString.value ?? "").toLowerCase())
          })
        }
        store.isParameterType = false
        const parameterIds = (await getSysParamsbyCategoryId(categoryId)).map(param => param.parameter_id)
        return parameterIds.filter(param => {
          return param.toLowerCase().includes((currentQueryString.value ?? "").toLowerCase())
        })
      }
    },
  })

  // ... 後面的代碼保持不變
}
```

這樣修改後，無論輸入的文字或選項中的文字是大寫還是小寫，都可以正確地進行過濾。例如，如果輸入 "ab"，它將匹配 "Ab"、"AB"、"ab" 等。

請嘗試這個修改，看看是否解決了你的問題。如果還有其他問題，請告訴我，我會很樂意進一步幫助你。


💬:
