---
date: 2024-11-11
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Example to store enum in fields of structs

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
    //         ^^^^^  This is the enum we created above,
    //                here we use it as a regular type
}

impl Catalog {
    fn new() -> Self {
        Catalog { items: vec![] }
    }
    fn add(&mut self, item: Media) {
    //                      ^^^^^ 
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

