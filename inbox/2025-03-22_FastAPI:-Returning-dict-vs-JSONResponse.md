---
date: 2025-03-22
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# FastAPI: Returning dict vs JSONResponse

```python
from fastapi import FastAPI
from fastapi.responses import JSONResponse, PlainTextResponse
from typing import Dict, List, Any

app = FastAPI()

# 1. 直接返回 dict (或其他 Python 對象)
@app.get("/api/dict-return")
async def get_dict_data() -> Dict[str, Any]:
    """
    直接返回 Python 字典。
    FastAPI 會自動將其轉換為 JSON 響應，狀態碼默認為 200 OK。
    """
    return {
        "message": "Hello World",
        "items": [1, 2, 3],
        "success": True
    }
    # FastAPI 會自動:
    # 1. 將 dict 轉換為 JSON
    # 2. 設置 Content-Type: application/json
    # 3. 設置狀態碼為 200 OK

# 2. 返回 JSONResponse
@app.get("/api/json-response")
async def get_json_response():
    """
    返回 JSONResponse 對象。
    這允許更精確地控制響應的各個方面。
    """
    content = {
        "message": "Hello World",
        "items": [1, 2, 3],
        "success": True
    }
    
    return JSONResponse(
        content=content,  # 響應內容
        status_code=200,  # 自定義狀態碼
        headers={         # 自定義頭部
            "X-Custom-Header": "custom-value",
            "Cache-Control": "max-age=60"
        }
    )

# 3. 何時使用 JSONResponse 而不是直接返回 dict

# 3.1 需要自定義狀態碼時
@app.get("/api/custom-status")
async def get_with_custom_status():
    """當需要返回非 200 狀態碼但仍然是成功響應時"""
    return JSONResponse(
        content={"message": "Resource created successfully"},
        status_code=201  # Created
    )

# 3.2 需要自定義頭部時
@app.get("/api/custom-headers")
async def get_with_custom_headers():
    """當需要設置自定義 HTTP 頭部時"""
    return JSONResponse(
        content={"message": "Data with custom headers"},
        headers={
            "X-Rate-Limit": "60",
            "X-Cache-Control": "max-age=3600"
        }
    )

# 3.3 需要流式響應時
@app.get("/api/streaming")
async def get_streaming_response():
    """
    當需要流式返回大量數據時，避免一次性加載全部數據到內存
    """
    async def generate_data():
        yield '{"results": ['
        for i in range(1, 1001):
            yield f'{{"id": {i}}}{"," if i < 1000 else ""}'
        yield ']}'
    
    return JSONResponse(content=generate_data())

# 3.4 需要設置 cookies 時
@app.get("/api/with-cookies")
async def get_with_cookies():
    """當需要在響應中設置 cookies 時"""
    response = JSONResponse(content={"message": "Response with cookies"})
    response.set_cookie(key="session", value="abc123", httponly=True)
    return response

# 4. 使用其他響應類型
@app.get("/api/text-response")
async def get_text_response():
    """返回純文本響應而非 JSON"""
    return PlainTextResponse(
        content="This is a plain text response",
        status_code=200,
        headers={"X-Content-Type": "text/plain"}
    )

# 5. 在路由函數中使用類型提示
@app.get("/api/typed-dict-return")
async def get_typed_dict() -> Dict[str, Any]:
    """
    使用類型提示的 dict 返回。
    FastAPI 會使用這些類型提示來生成 OpenAPI 文檔。
    """
    return {"message": "This is a typed dict return"}

# 6. 使用 Pydantic 模型
from pydantic import BaseModel

class Item(BaseModel):
    id: int
    name: str
    price: float
    is_offer: bool = False

@app.get("/api/model-return", response_model=Item)
async def get_model_return():
    """
    返回符合 Pydantic 模型的 dict。
    FastAPI 會驗證返回的數據符合模型定義。
    """
    # 可以直接返回 dict，FastAPI 會將其轉換為 Item 模型
    return {
        "id": 1,
        "name": "Example",
        "price": 9.99,
        "is_offer": True
    }
    
    # 或者返回 Pydantic 模型實例
    # return Item(id=1, name="Example", price=9.99, is_offer=True)
```

所以，當你需要更精細地控制響應時，可以使用 `JSONResponse` 類，而當只需要返回 JSON 數據時，直接返回 dict 即可

