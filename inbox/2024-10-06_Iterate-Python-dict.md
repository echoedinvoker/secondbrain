---
date: 2024-10-06
type: fact
aliases:
  -
hubs:
  - "[[Python]]"
---

#dict #iterate

# Iterate Python dict

```python
dummy_dict = {
    'name': 'John Doe',
    'age': 30,
    'location': 'New York'
}

list_of_key_value_pairs = dummy_dict.items()  # [('name', 'John Doe'), ('age', 30), ('location', 'New York')]

for key, value in list_of_key_value_pairs:
    print(key, value)
```
