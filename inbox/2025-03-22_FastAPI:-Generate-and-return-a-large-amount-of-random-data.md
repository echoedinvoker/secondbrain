---
date: 2025-03-22
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# FastAPI: Generate and return a large amount of random data

```python
from fastapi import FastAPI, Query
from fastapi.responses import JSONResponse
from pydantic import BaseModel, Field
from typing import List, Optional
import random
import string
import time

app = FastAPI()

# 定義數據模型
class Item(BaseModel):
    id: int
    name: str
    value: float
    tags: List[str] = Field(default_factory=list)
    is_active: bool = True

# 生成隨機項目的輔助函數
def generate_random_item(item_id: int) -> Item:
    # 生成隨機名稱
    name = f"Item_{(''.join(random.choices(string.ascii_letters, k=5)))}"
    
    # 生成隨機值
    value = round(random.uniform(1.0, 1000.0), 2)
    
    # 生成隨機標籤
    num_tags = random.randint(0, 3)
    tags = [''.join(random.choices(string.ascii_lowercase, k=4)) for _ in range(num_tags)]
    
    # 隨機活動狀態
    is_active = random.choice([True, False])
    
    return Item(id=item_id, name=name, value=value, tags=tags, is_active=is_active)

# 基本路由 - 返回固定數量的隨機數據
@app.get("/api/random-items", response_model=List[Item])
async def get_random_items(count: int = Query(default=10, ge=1, le=1000)):
    """
    生成並返回指定數量的隨機項目。
    
    - count: 要生成的項目數量 (1-1000)
    """
    items = [generate_random_item(i) for i in range(1, count + 1)]
    return items

# 分頁路由 - 處理大量數據的更好方法
@app.get("/api/paged-items")
async def get_paged_items(
    page: int = Query(default=1, ge=1),
    page_size: int = Query(default=100, ge=1, le=1000)
):
    """
    返回分頁的隨機項目。
    
    - page: 頁碼 (從1開始)
    - page_size: 每頁項目數 (1-1000)
    """
    # 計算起始和結束索引
    start_idx = (page - 1) * page_size + 1
    end_idx = start_idx + page_size
    
    # 生成當前頁的項目
    items = [generate_random_item(i) for i in range(start_idx, end_idx)]
    
    # 構建包含分頁信息的響應
    response = {
        "page": page,
        "page_size": page_size,
        "total_pages": 100,  # 在實際應用中，這應該基於總記錄數計算
        "total_items": 10000,  # 假設總共有10000條記錄
        "items": items
    }
    
    return response

# 流式響應 - 用於非常大的數據集
@app.get("/api/stream-items")
async def stream_items(count: int = Query(default=10000, ge=1, le=100000)):
    """
    使用流式響應生成大量隨機項目。
    這避免了在內存中一次性生成所有項目。
    
    - count: 要生成的項目總數
    """
    # 自定義響應生成器
    async def generate_items():
        yield "["  # 開始JSON數組
        
        for i in range(1, count + 1):
            item = generate_random_item(i)
            json_item = item.model_dump_json()
            
            if i < count:
                yield json_item + ","
            else:
                yield json_item
            
        yield "]"  # 結束JSON數組
    
    # 返回流式響應
    return JSONResponse(content=generate_items())

# 帶延遲的路由 - 模擬真實世界的API行為
@app.get("/api/delayed-items")
async def get_delayed_items(
    count: int = Query(default=100, ge=1, le=1000),
    delay_ms: int = Query(default=0, ge=0, le=5000)
):
    """
    返回隨機項目，但有意引入延遲以模擬真實世界的API行為。
    
    - count: 要生成的項目數量
    - delay_ms: 人為延遲(毫秒)
    """
    # 模擬處理延遲
    if delay_ms > 0:
        time.sleep(delay_ms / 1000)
    
    items = [generate_random_item(i) for i in range(1, count + 1)]
    return items

# 帶緩存的路由 - 提高性能
from fastapi import Depends
from fastapi.responses import JSONResponse
import hashlib
from datetime import datetime, timedelta

# 簡單的內存緩存
cache = {}

def get_cache_key(count: int) -> str:
    """生成緩存鍵"""
    return f"items_{count}"

@app.get("/api/cached-items")
async def get_cached_items(
    count: int = Query(default=1000, ge=1, le=10000),
    use_cache: bool = Query(default=True)
):
    """
    返回可能從緩存中獲取的隨機項目。
    
    - count: 要生成的項目數量
    - use_cache: 是否使用緩存
    """
    cache_key = get_cache_key(count)
    
    # 檢查緩存
    if use_cache and cache_key in cache:
        cache_entry = cache[cache_key]
        # 檢查緩存是否仍然有效 (10分鐘)
        if datetime.now() < cache_entry["expires"]:
            return JSONResponse(
                content=cache_entry["data"],
                headers={"X-Cache": "HIT"}
            )
    
    # 生成新數據
    items = [generate_random_item(i).model_dump() for i in range(1, count + 1)]
    
    # 更新緩存
    if use_cache:
        cache[cache_key] = {
            "data": items,
            "expires": datetime.now() + timedelta(minutes=10)
        }
    
    return JSONResponse(
        content=items,
        headers={"X-Cache": "MISS"}
    )
```

