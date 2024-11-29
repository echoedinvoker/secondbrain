---
date: 2024-11-12
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Three ways to create module

## Requirements

```rust
#[derive(Debug)]
enum Media {
    Book { title: String, authour: String },
    Movie { title: String, director: String },
    Audiobook { title: String },
}

impl Media {
    fn description(&self) -> String {
        match self {
            Media::Book { title, authour } => format!("{} by {}", title, authour),
            Media::Movie { title, director } => format!("{} by {}", title, director),
            Media::Audiobook { title } => format!("{} by {}", title, "unknown"),
        }
    }
}

#[derive(Debug)]
struct Catalog {
    items: Vec<Media>,
}

impl Catalog {
    fn new() -> Self {
        Catalog { items: vec![] }
    }
    fn add(&mut self, item: Media) {
        self.items.push(item);
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

    let mut catalog = Catalog::new();

    catalog.add(book);
    catalog.add(movie);
    catalog.add(audiobook);

    println!("{:#?}", catalog);
    for item in &catalog.items {
        println!("{}", item.description());
    }
}
```

We feel that the logic of the above codes has become too complicated, so we want to use modules to increase readability. We can establish modules in three ways.


## Create module in the same file

[[2024-11-12_Create-module-in-the-same-file|Create module in the same file]]


## Create module in a new single file

[[2024-11-12_Create-module-in-a-new-single-file|Create module in a new single file]]


## Create module with multiple files in a nested directory

[[2024-11-12_Create-module-with-nested-file-structure|Create module with nested file structure]]
