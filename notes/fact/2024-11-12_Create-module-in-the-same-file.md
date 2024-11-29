---
date: 2024-11-12
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create module in the same file

[[2024-11-12_Three-ways-to-create-module|here]] is original source. We want to create a module in the same file to improve the code organization.

```rust
mod content {  // create module with name `content`
    #[derive(Debug)]
    pub enum Media {
//  ^^^ anything in the module is private by default,
//      which means that it can't be accessed from outside the module.
//      so we need to make it public by using `pub` keyword
        
        Book { title: String, authour: String },
        Movie { title: String, director: String },
        Audiobook { title: String },
    }

    impl Media {
        pub fn description(&self) -> String {
  //    ^^^ even though the enum is public, the method is still private by default
  //        so we need to make it public by using `pub` keyword separately
            match self {
                Media::Book { title, authour } => format!("{} by {}", title, authour),
                Media::Movie { title, director } => format!("{} by {}", title, director),
                Media::Audiobook { title } => format!("{} by {}", title, "unknown"),
            }
        }
    }

    #[derive(Debug)]
    pub struct Catalog {
//  ^^^
        pub items: Vec<Media>,
  //    ^^^ even though the struct is public, the field is still private by default
  //        so we need to make it public by using `pub` keyword separately
    }

    impl Catalog {
        pub fn new() -> Self {
    //  ^^^
            Catalog { items: vec![] }
        }
        pub fn add(&mut self, item: Media) {
    //  ^^^
            self.items.push(item);
        }
    }
}

fn main() {
    let book = content::Media::Book {
    //         ^^^^^^^^^ start with the module name `content::`
        title: String::from("The Catcher in the Rye"),
        authour: String::from("J.D. Salinger"),
    };

    let movie = content::Media::Movie {
    //          ^^^^^^^^^
        title: String::from("The Godfather"),
        director: String::from("Francis Ford Coppola"),
    };

    let audiobook = content::Media::Audiobook {
    //              ^^^^^^^^^
        title: String::from("The Great Gatsby"),
    };

    let mut catalog = content::Catalog::new();
    //                ^^^^^^^^^

    catalog.add(book);
    catalog.add(movie);
    catalog.add(audiobook);

    println!("{:#?}", catalog);
    for item in &catalog.items {
        println!("{}", item.description());
    }
}

```
We can simply move content insides `mod content { ... }` to another file and import it using `mod content;` in the main file, which is [[2024-11-12_Create-module-in-a-new-single-file|second way]] to create module.
```rust
