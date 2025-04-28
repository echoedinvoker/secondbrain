---
date: 2025-03-21
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Implement debounce functionality in mutationFn

當需要在保持 useMutationState 功能的同時實現 debounce 時，可以在 mutationFn 中實現 debounce 邏輯：

```typescript
import { useMutation } from "@tanstack/vue-query";
import { ref } from "vue";

export function useDebounceInMutationFn() {
  // 用於追蹤 debounce 狀態
  const debouncePromiseRef = ref<Promise<any> | null>(null);
  const debounceResolveRef = ref<((value: any) => void) | null>(null);
  const debounceTimerRef = ref<number | null>(null);
  
  return useMutation({
    mutationKey: ['debounced-mutation'],
    mutationFn: (variables) => {
      // 如果已有計時器，清除它但保持 Promise
      if (debounceTimerRef.value !== null) {
        clearTimeout(debounceTimerRef.value);
      }
      
      // 如果沒有現有的 Promise，創建一個新的
      if (!debouncePromiseRef.value) {
        debouncePromiseRef.value = new Promise((resolve) => {
          debounceResolveRef.value = resolve;
        });
      }
      
      // 設置新的計時器
      debounceTimerRef.value = setTimeout(() => {
        // 實際執行 API 調用
        const result = api.updateData(variables);
        
        // 解析 Promise
        if (debounceResolveRef.value) {
          result.then(debounceResolveRef.value);
        }
        
        // 重置狀態
        debouncePromiseRef.value = null;
        debounceResolveRef.value = null;
        debounceTimerRef.value = null;
      }, 1000) as unknown as number;
      
      // 返回 Promise，這樣 mutation 狀態會保持 pending 直到實際 API 調用完成
      return debouncePromiseRef.value;
    }
  });
}
```

這種方法的優點是：
1. 保持了 TanStack Query 的 mutation 狀態管理功能
2. useMutationState 可以正確獲取 pending 中的 mutation 數據
3. 實現了 debounce 功能，避免頻繁 API 調用

