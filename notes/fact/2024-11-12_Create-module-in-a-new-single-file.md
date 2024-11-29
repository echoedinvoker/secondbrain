---
date: 2024-11-12
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create module in a new single file

[[2024-11-12_Three-ways-to-create-module|here]] is source codes, we want to create a module in a new single file to improve the code organization.

```rust
// src/main.rs

mod content; // include the module `content` from `content.rs`

fn main() {
    let book = content::Media::Book {
        title: String::from("The Catcher in the Rye"),
        authour: String::from("J.D. Salinger"),
    };

    let movie = content::Media::Movie {
        title: String::from("The Godfather"),
        director: String::from("Francis Ford Coppola"),
    };

    let audiobook = content::Media::Audiobook {
        title: String::from("The Great Gatsby"),
    };

    let mut catalog = content::Catalog::new();

    catalog.add(book);
    catalog.add(movie);
    catalog.add(audiobook);

    println!("{:#?}", catalog);
    for item in &catalog.items {
        println!("{}", item.description());
    }
}
```

```rust
// src/content.rs   <---- module name will be the same as the file name `content` automatically

#[derive(Debug)]
pub enum Media {  // don't forget to make it public with keyword `pub`
    Book { title: String, authour: String },
    Movie { title: String, director: String },
    Audiobook { title: String },
}

impl Media {
    pub fn description(&self) -> String {
        match self {
            Media::Book { title, authour } => format!("{} by {}", title, authour),
            Media::Movie { title, director } => format!("{} by {}", title, director),
            Media::Audiobook { title } => format!("{} by {}", title, "unknown"),
        }
    }
}

#[derive(Debug)]
pub struct Catalog {
    pub items: Vec<Media>,
}

impl Catalog {
    pub fn new() -> Self {
        Catalog { items: vec![] }
    }
    pub fn add(&mut self, item: Media) {
        self.items.push(item);
    }
}

```
