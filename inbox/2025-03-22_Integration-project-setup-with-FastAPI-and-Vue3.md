---
date: 2025-03-22
type: fact
aliases:
  -
hubs:
  - "[[fullstack]]"
---

# Integration project setup with FastAPI and Vue3

```bash
# 創建後端目錄
mkdir -p my-project/backend/app
cd my-project/backend

# 創建虛擬環境
python -m venv venv
source venv/bin/activate  # Linux/Mac

# 安裝依賴
pip install fastapi python-dotenv "fastapi[standard]"
#                                 ^^^^^^^^^^^^^^^^^^^ for fastapi cli, it'll install uvicorn, or you can just use uvicorn

# 創建FastAPI應用
v app/main.py
```
```python
# 基本FastAPI後端設置
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI(title="API名稱")

# 配置CORS以允許前端訪問
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:5173"],  # Vue3 默認開發端口
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

# 基本路由示例
@app.get("/api/items")
async def get_items():
    return [{"id": 1, "name": "Item 1"}, {"id": 2, "name": "Item 2"}]

@app.post("/api/items")
async def create_item(item: dict):
    return {"id": 3, **item}  # 實際應用中會存儲到數據庫
```

#### Vue3與FastAPI整合

```bash
cd my-project
npm create vite@latest frontend -- --template vue
cd frontend
npm install

# edit src/App.vue
v src/App.vue
```

```vue
<script setup>
import { ref, onMounted } from 'vue'

const items = ref([])
const newItemName = ref('')
const loading = ref(false)

// 從FastAPI獲取數據
async function fetchItems() {
  loading.value = true
  try {
    const response = await fetch('http://localhost:8000/api/items')
    items.value = await response.json()
  } catch (err) {
    console.error('Error:', err)
  } finally {
    loading.value = false
  }
}

// 發送數據到FastAPI
async function addItem() {
  // 檢查輸入不為空
  if (!newItemName.value.trim()) return
  
  loading.value = true
  try {
    const response = await fetch('http://localhost:8000/api/items', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name: newItemName.value })
    })
    const newItem = await response.json()
    items.value.push(newItem)
    newItemName.value = ''  // 清空輸入框
  } catch (err) {
    console.error('Error adding item:', err)
  } finally {
    loading.value = false
  }
}

onMounted(fetchItems)
</script>

<template>
  <div>
    <!--loading indicator-->
    <div v-if="loading">載入中...</div>
    
    <!--add form-->
    <div class="add-form">
      <input 
        v-model="newItemName" 
        placeholder="輸入新項目名稱" 
        @keyup.enter="addItem"
      />
      <button @click="addItem">添加</button>
    </div>
    
    <!--items list-->
    <ul>
      <li v-for="item in items" :key="item.id">{{ item.name }}</li>
    </ul>
  </div>
</template>

<style scoped>
.add-form {
  margin: 20px 0;
  display: flex;
}
input {
  flex: 1;
  padding: 8px;
  margin-right: 10px;
}
button {
  padding: 8px 16px;
}
</style>
```
