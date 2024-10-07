---
date: 2024-10-06
type: fact
aliases:
  -
hubs:
  - "[[Python]]"
---

# Set value of Python object attribute

## Three ways to set the value of an attribute in Python

**Dot notation**: 
```python
object.attribute = value`
```
THe attribute must be defined before runtime, otherwise, it will raise an `AttributeError`.

**Setattr()**: 
```python
setattr(object, 'attribute', value)
```
Most objects can be used without needing to define attributes in advance.

**Dictionary notation**:
```python
object['attribute'] = value
```
It relies on the dict object or `__setattr__` method of the object. If your object don't have a `__setattr__` method, you can't use this method.

## Conclusion

`setattr()` is the most flexible way to set the value of an attribute in Python. It is also the most common way to set the value of an attribute in Python. 
```
