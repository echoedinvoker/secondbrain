---
date: 2024-11-18
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create a fn to filter colors by length

```rust
fn filter_by_len(elements: &[String], len: usize) -> Vec<String> {
    elements
        .iter()
        .filter(|el| el.len() < len)  // `.filter()` is iterator adaptor, return Option::Some(el) if return is true
                                      // return Option::None if return is false
        .map(|el| el.to_string())
        //    ^^ `el` will get above `el` if above result is Option::Some(el)   (unwrap automatically)
        //       this method won't get called if above result is Option::None   (break the iteration pipeline)
        .collect()
}

fn main() {
    let colors = vec![
        String::from("Red"),
        String::from("Green"),
        String::from("Blue"),
    ];

    let filtered_colors = filter_by_len(&colors, 5);

    println!("{:#?}", filtered_colors);
}

```

Some iterator consumers will return an Option value. If it is Some, `el` will continue to be passed down. If it is None, the iteration pipeline will be broken.

This example and [[2024-11-18_Create-a-fn-to-find-a-matching-element-or-return-a-fallback|find match case]] both using Option, but the concepts used are very different:

**filter case**
- uses iterator adaptor `filter`, all methods used are related to iterators
- `Option` is return of `filter` iterator **adaptor**


**find match case**
- uses the Option method `map_or`, which is not related to iterators
- `Option` is return of `find` iterator **consumer**
