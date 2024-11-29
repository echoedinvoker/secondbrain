---
date: 2024-11-12
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Create a method which may or may not return a value manually

From [[2024-11-12_Rust-built-in-Option-enum,-taking-care-of-may-or-may-not-getting-thing-issue|this topic]] introducing the built-in enum `Option` in Rust, you may still not have a clear understanding of the enum `Option`, so below we will demonstrate the process of why enum `Option` was designed in the first place.

## Requirements

```rust
#[derive(Debug)]
enum Media {
    Book { title: String, authour: String },
    Movie { title: String, director: String },
    Audiobook { title: String },
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

    // create a method 'get_by_index' to return a reference of a specific item by index
    // not allow to use built-in enum Option
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

    // let first_item = catalog.get_by_index(0);
    // let none_exist_item = catalog.get_by_index(5);
}
```

## Create custom enum MightHaveAValue

This question is to help us better understand why the `Option` enum was designed.

```rust
    fn get_by_index(&self, index: usize) -> &Media {
    //                                      ^^^^^^ we must return reference of Media value
        if index < self.items.len() {
            &self.items[index]
        } else {
            // there is no way to return reference of Media value here
            // And there is no null, nill or undefined in Rust
        }
    }
```

To solve the above dilemma, we must create an enum to replace the return type `&Media`.

```rust
enum MightHaveAValue {
    ThereIsAValue(&Media),
    NoValueAvailable,
}
```
because of lifetime annotation, we need to do a bit modification to the enum.
```rust
enum MightHaveAValue<'a> {
//                  ^^^^
    ThereIsAValue(&'a Media),
    //             ^^^
    NoValueAvailable,
}
```


## Replace return type with custom enum, then everything is done

Then we replace return type `&Media` of method `get_by_index` with it:

```rust
#[derive(Debug)]
enum Media {
    Book { title: String, authour: String },
    Movie { title: String, director: String },
    Audiobook { title: String },
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

    fn get_by_index(&self, index: usize) -> MightHaveAValue {
    //                                      ^^^^^^^^^^^^^^^ relace &Media
        if index < self.items.len() {
            MightHaveAValue::ThereIsAValue(&self.items[index])
        //  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
        } else {
            MightHaveAValue::NoValueAvailable
        //  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ we can always return enum MightHaveAValue
        }
    }
}

enum MightHaveAValue<'a> {
    ThereIsAValue(&'a Media),
    NoValueAvailable,
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

    match catalog.get_by_index(0) {
        MightHaveAValue::ThereIsAValue(media) => println!("Found media: {:?}", media),  // match
        MightHaveAValue::NoValueAvailable => println!("No media found"),
    }  // output: Found media: Book { title: "The Catcher in the Rye", authour: "J.D. Salinger" }

    match catalog.get_by_index(5) {
        MightHaveAValue::ThereIsAValue(media) => println!("Found media: {:?}", media),
        MightHaveAValue::NoValueAvailable => println!("No media found"),  // match
    }  // output: No media found
}

```

In the above case, the enum MightHaveAValue is actually the enum Option. We demonstrated the process of designing the enum Option.


## Repace custom enum with built-in enum Option back

```rust
#[derive(Debug)]
enum Media {
    Book { title: String, authour: String },
    Movie { title: String, director: String },
    Audiobook { title: String },
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

    fn get_by_index(&self, index: usize) -> Option<&Media> {
    //                                      ^^^^^^^^^^^^^^ use built-in enum Option
    //                                                     and need to specify value type 
        if index < self.items.len() {
            Some(&self.items[index])
        //  ^^^^
        } else {
            None
        //  ^^^^ no need to type Option::Some or Option::None, just Some and None, which is more concise
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

    let mut catalog = Catalog::new();

    catalog.add(book);
    catalog.add(movie);
    catalog.add(audiobook);

    match catalog.get_by_index(0) {
        Some(media) => println!("Found media: {:?}", media),
    //  ^^^^
        None => println!("No media found"),
    //  ^^^^
    }

    match catalog.get_by_index(5) {
        Some(media) => println!("Found media: {:?}", media),
    //  ^^^^
        None => println!("No media found"),
    /   ^^^^
    }
}

```
