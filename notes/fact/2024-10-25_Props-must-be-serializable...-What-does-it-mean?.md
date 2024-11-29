---
date: 2024-10-25
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Props must be serializable... What does it mean?


## 可序列化與不可序列化的資料

**可序列化資料**

1. 原始類型：數字、字符串、布爾值、null
2. 數組
3. 純對象（只包含可序列化的值的對象）

**不可序列化資料**

1. 函數
2. Symbol
3. undefined
4. 循環引用的對象
5. 特殊對象（如 Date 對象，雖然可以被 JSON.stringify() 處理，但會丟失類型信息）


## 為什麼某些情況下 props 需要是可序列化的

1. 服務器端渲染 (SSR)：
   在 Next.js 等框架中，組件可能需要在服務器上渲染，然後將渲染結果發送到客戶端。在這個過程中，組件的 props 需要從服務器傳輸到客戶端。只有可序列化的數據才能被正確地傳輸和重建。

2. 狀態管理：
   某些狀態管理庫（如 Redux）需要能夠序列化整個應用狀態，以支持功能如時間旅行調試或狀態持久化。

3. 網絡請求：
   當你需要通過網絡發送數據（如 API 請求）時，數據需要是可序列化的。

4. 持久化存儲：
   如果你想將應用狀態保存到 localStorage 或 IndexedDB 中，數據必須是可序列化的。

5. 工作線程通信：
   在主線程和 Web Worker 之間傳遞消息時，數據必須是可序列化的。

>基本上 Next.js 中無法傳送 function 這種不可序列化的數據到 client component (server component 允許), 但是一般前後端分離的 React, Vue 卻不會有這個問題。


## 如果要使用 props 傳送不可序列化的數據，但出現需要可序列化的錯誤怎麼辦？

>改傳送 props 的 ID，而不是整個對象。在組件中，通過 ID 查找對象。

範例:
```tsx
'use client';  // client component

interface Props {
  fecthData: () => Promise<any>;  // 不可序列化的數據傳送到 client component
}                                 // 通常會出現 props 需要可序列化的錯誤

export default function MyComponent({ fetchData }: Props) {
  const [data, setData] = useState<any>(null);
  useEffect(() => {
    fetchData().then((res) => setData(res));
  }, [fetchData]);
  return <div>{data}</div>;
}

```
可以改為:

```tsx
use 'client'; 

interface Props {
  fetchDataId: string;  // 傳送可序列化的數據：ID
}

export default function MyComponent({ fetchDataId }: Props) {
  const [data, setData] = useState<any>(null);
  useEffect(() => {
    fetchData(fetchDataId).then((res) => setData(res));  // fetch data by ID
  }, [fetchDataId]);
  return <div>{data}</div>;
}
```
