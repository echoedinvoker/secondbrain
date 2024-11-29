---
date: 2024-11-12
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create module with nested file structure

In Rust, any files or folders will be treated as modules if they are placed in a `src` directory. 

There must be a `mod.rs` file under the folder in order to be treated as a module.

```bash
src
├── content  # folder need a file `mod.rs` to be treated as module
│   ├── catalog.rs # this is a module `catalog`
│   ├── media.rs  # this is a module `media`
│   └── mod.rs  # this is special file which makes the current folder as module `content`
└── main.rs  # root module
```

We use above structure to organize [[2024-11-12_Three-ways-to-create-module|our source codes]] to improve readability and maintainability.

```rust
// src/content/media.rs 

#[derive(Debug)]
pub enum Media {
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
```

```rust
// src/content/catalog.rs 

use super::media::Media; // to include sibling module `media`
                         // `super` is used to refer parent module `content`
                         // not `mod` but `use` here, be careful!!

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

```rust
// src/content/mod.rs 

pub mod catalog;  // `mod` means to import, `pub` means to export
pub mod media;
```

```rust
// src/main.rs 

mod content; // import module `content`
             // we can't directly import `content/catalog` or `content/media`

fn main() {
    let book = content::media::Media::Book {
    //         ^^^^^^^^^^^^^^^^^^^^^^^ a bit tedious to write
        title: String::from("The Catcher in the Rye"),
        authour: String::from("J.D. Salinger"),
    };

    let movie = content::media::Media::Movie {
        title: String::from("The Godfather"),
        director: String::from("Francis Ford Coppola"),
    };

    let audiobook = content::media::Media::Audiobook {
        title: String::from("The Great Gatsby"),
    };

    let mut catalog = content::catalog::Catalog::new();

    catalog.add(book);
    catalog.add(movie);
    catalog.add(audiobook);

    println!("{:#?}", catalog);
    for item in &catalog.items {
        println!("{}", item.description());
    }
}
```

We can use `use` to simplify the code as below:

```rust
mod content;

use content::catalog::Catalog; // use `use` to simplify the code
use content::media::Media;     // `use` doesn't mean to import, but to simplify the code

fn main() {
    let book = Media::Book {
    //         ^^^^^^^
        title: String::from("The Catcher in the Rye"),
        authour: String::from("J.D. Salinger"),
    };

    let movie = Media::Movie {
    //          ^^^^^^^
        title: String::from("The Godfather"),
        director: String::from("Francis Ford Coppola"),
    };

    let audiobook = Media::Audiobook {
    //              ^^^^^^^
        title: String::from("The Great Gatsby"),
    };

    let mut catalog = Catalog::new();
    //                ^^^^^^^^^

    catalog.add(book);
    catalog.add(movie);
    catalog.add(audiobook);

    println!("{:#?}", catalog);
    for item in &catalog.items {
        println!("{}", item.description());
    }
}

