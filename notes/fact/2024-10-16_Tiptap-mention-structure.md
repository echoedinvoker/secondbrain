---
date: 2024-10-16
type: fact
aliases:
  -
hubs:
  - "[[Tiptap]]"
---

# Tiptap mention structure
 
```
+-------------------+
|   QueryEditor.vue |
|  (Main Component) |
+--------+----------+
         |
         | Uses
         v
+-------------------+    Uses     +-------------------+
|   suggestions.ts  |<----------->|   MentionList.vue |
| (Mention Config)  |             | (Suggestion List) |
+--------+----------+             +--------+----------+
         |                                 |
         | Configures                      | Uses
         v                                 v
+-------------------+             +-------------------+
| Tiptap Editor     |             | useSuggestions.ts |
| with Mention      |             |    (Data Hook)    |
+-------------------+             +--------+----------+
                                           |
                                           | Interacts with
                                           v
                                  +-------------------+
                                  | suggestionsStore  |
                                  | (Pinia Store)     |
                                  +-------------------+
                                           ^
                                           |
                                           | Interacts with
                                           |
                            +----------------------------------+
                            | API Services                     |
                            | (getSysParamsTypeByName,         |
                            |  getSysParamsTypeByParentTypeId, |
                            |  getSysParamsbyCategoryId)       |
                            +----------------------------------+
```

這個圖形展示了各個組件和文件之間的關係：

1. QueryEditor.vue 是主要組件，它使用 suggestions.ts 來配置 Tiptap 編輯器的 Mention 功能。

2. suggestions.ts 配置了 MentionList.vue 作為建議列表的渲染組件。

3. MentionList.vue 使用 useSuggestions.ts hook 來獲取建議數據。

4. useSuggestions.ts 與 suggestionsStore (Pinia store) 交互，管理建議相關的狀態。

5. useSuggestions.ts 還調用 API 服務來獲取實際的建議數據。

6. suggestionsStore 被 MentionList.vue 和 useSuggestions.ts 共同使用，用於共享狀態。

這個架構展示了數據流和組件之間的關係，突出了各部分如何協同工作以實現自動完成功能。


