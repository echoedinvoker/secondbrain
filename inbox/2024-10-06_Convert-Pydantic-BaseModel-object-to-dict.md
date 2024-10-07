---
date: 2024-10-06
type: fact
aliases:
  -
hubs:
  - "[[Python]]"
---

#Pydantic #dict

# Convert Pydantic BaseModel object to dict

## before Pydantic 2

```python
dict_obj = pydantic_obj.dict()
```

## Pydantic 2 and later

```python
dict_obj = pydantic_obj.model_dump()
```

