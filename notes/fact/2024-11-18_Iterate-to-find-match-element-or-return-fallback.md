---
date: 2024-11-18
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Iterate to find match element or return fallback

After [[2024-11-18_Decide-types-of-each-argument-of-finding-a-matching-element-function|types of fn]] are decided, we can implement the content of the function:

```rust
fn find_color_or(elements: &[String], search: &str, fallback: &str) -> String {
    elements
        .iter()
        .find(|&el| el == search) // `.find()` is a special iterator consumer
                                  // it will call `.next()` until its closure returns `true`
                                  // when closure returns `true` it will return Option::Some(el)
                                  // if closure never returns `true` it will return Option::None

        .map_or( // `.map_or()` is a method of Option, it is not do anything for the iterator
            String::from(fallback),  // if Option::None it will return this
            |el| el.to_string()  // if Option::Some(el) it will unwrap the value and pass it to this closure
        //         ^^^^^^^^^^^^
        //         because `el` is a reference, we need to convert it to a String to fit the return type
        )
}

fn main() {
    let colors = vec![
        String::from("Red"),
        String::from("Green"),
        String::from("Blue"),
    ];

    let found_color = find_color_or(&colors, "Green", "Yellow");
    //                                       ^^^^^^^  ^^^^^^^^ we create `string slice` by using double quotes
    //                                                         remember "TEXT" is &str (a reference), not String (a value)
    //                                                         which is different from other languages, take care of this!

    println!("{}", found_color);
}

```

Be especially careful with `.map_or`, do not confuse it with `.map`, the former has nothing to do with iterators, it just works with `Option` type.

In addition, `.find` is also a special iterator consumer because it returns an `Option` type value.
