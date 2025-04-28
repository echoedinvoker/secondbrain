---
date: 2025-03-29
type: fact
aliases:
  -
hubs:
  - "[[fastapi]]"
---

# Assertions to validate different things

Below tests are all valid and will pass.

## Validate Integers equality
```py
def test_integers_equality():
    assert 1 == 1
    assert 1 != 2
```

## Validate Instances
```py
def test_instances():
    assert isinstance(1, int)
    assert isinstance(1.0, float)
    assert isinstance('a', str)
```

## Validate Booleans
```py
def test_booleans():
    validated = True
    assert validated is True
    assert ('hello' == 'world') is False
```

## Validate Types
```py
def test_types():
    assert type('Hello' is str)
    assert type('world' is not int)
```

## Validate Greater than & Less than
```py
def test_greater_than_less_than():
    assert 2 > 1
    assert 1 < 2
    assert 1 >= 1
    assert 1 <= 1
```

## Validate Lists
```py
def test_list():
    num_list = [1, 2, 3]
    any_list = [False, False]
    assert 1 in num_list
    assert 7 not in num_list
    assert all(num_list)
    #      ^^^ to check if all elements are True
    #          in Python, only 0 is False
    assert not any(any_list)
    #          ^^^ to check if any element is True
```



