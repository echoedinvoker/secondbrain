---
date: 2024-11-14
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Why is there &String and &str?

`&String` exists because we want read-only references to `String` values, but it is not efficient.

That's why `&str` exists, which is a read-only reference to text data, and it is more efficient than `&String`.


## Reason 1: &str is more efficient than &String

![string-slice-to-text.png](../../assets/imgs/string-slice-to-text.png)

Assuming we cannot use `&str`, but we still want to generate a read-only reference to text data:

![string-refs-to-text.png](../../assets/imgs/string-refs-to-text.png)


It is obvious that `&str` is much more efficient than `&String`.


## Reason 2: &str can `slice` existing text data in heap

![slice-by-string-slice.png](../../assets/imgs/slice-by-string-slice.png)


If we want to slice a text data in heap but without using `&str`:

![slice-without-string-slice.png](../../assets/imgs/slice-without-string-slice.png)

Without `&str`, it is obvious that the whole process is much more complex and less efficient.


## Conclusion

Using the knowledge above, we can conclude [[2024-11-14_When-to-use-String,-&String-or-&str?|when to use String, &String or &str]].
