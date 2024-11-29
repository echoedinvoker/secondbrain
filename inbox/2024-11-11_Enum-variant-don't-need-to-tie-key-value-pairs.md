---
date: 2024-11-11
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Enum variant don't need to tie key value pairs

From [[2024-11-11_Model-several-different-things-that-are-all-kind-of-similar-by-enum|this topic]], we defined an enum and all its variant are tied to key value pairs data.

But in fact, we can only tie raw data to variants or even no data at all.

```rust
#[derive(Debug)]
enum Media {
    Book { title: String, authour: String },
    Movie { title: String, director: String },
    Audiobook { title: String },
    //        ^^^^^^^^^^^^^^^^^ key value pairs
    Page(u32),
    //   ^^^ raw data, use parentheses instead of curly braces
    Placeholder,
//  ^^^^^^^^^^^ no data tied to this variant, which is also valid
}

impl Media {
    fn description(&self) -> String {
        match self {
            Media::Book { title, authour } => format!("{} by {}", title, authour),
            Media::Movie { title, director } => format!("{} by {}", title, director),
            Media::Audiobook { title } => format!("{} by {}", title, "unknown"),
            Media::Page(page) => format!("Page {}", page),
            //         ^^^^^^
            Media::Placeholder => format!("Placeholder"),
            //  no any data for this variant, so no need to extract data
        }
    }
}

fn main() {
    let book = Media::Book {
        title: String::from("The Catcher in the Rye"),
        authour: String::from("J.D. Salinger"),
    };

    let movie = Media::Movie {
        title: String::from("The Godfather"),
        director: String::from("Francis Ford Coppola"),
    };

    let audiobook = Media::Audiobook {
        title: String::from("The Great Gatsby"),
    };

    let page = Media::Page(100);
    //                    ^^^^^

    let placeholder = Media::Placeholder;
    //                ^^^^^^^^^^^^^^^^^^ simply create an instance of this variant without any data

}

```
