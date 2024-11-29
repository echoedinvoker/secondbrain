---
date: 2024-11-11
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Implement methods on enum


## Requirements: implement methods on enum

```rust
#[derive(Debug)]
enum Media {
    Book { title: String, authour: String },
    Movie { title: String, director: String },
    Audiobook { title: String },
}

// because `Media` is a type, we can implement methods on it like we would for a struct
impl Media {
    fn description(&self) -> String {
        // return a string based on the variant
        // they are slightly different from each other
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
}
```

## Downfall of enums: we need to know which variant we are working with before we can do anything with it

```rust
#[derive(Debug)]
enum Media {
    Book { title: String, authour: String },
    Movie { title: String, director: String },
    Audiobook { title: String },
}

impl Media {
    fn description(&self) -> String {
        self.title
    //  ^^^^^^^^^^ Even though we know that all variants have a title, Rust still raises an error here
    //             We must actively check which variant we are working with before we can do anything with it
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
}

```

## There are two ways to check which variant we are working with

**if else**

```rust
#[derive(Debug)]
enum Media {
    Book { title: String, authour: String },
    Movie { title: String, director: String },
    Audiobook { title: String },
}

impl Media {
    fn description(&self) -> String {

        // use `if ... else if ... else ...` to check which variant we are working with
        if let Media::Book { title, authour } = self {
        // ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ it means if self is a `Media::Book`,
        //                                           put the `title` and `author` to be used in the block
            format!("{} by {}", title, authour)
        } else if let Media::Movie { title, director } = self {
            format!("{} by {}", title, director)
        } else if let Media::Audiobook { title } = self {
            format!("{} (audiobook)", title)
        } else {
            String::from("Unknown")
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

    println!("{}", book.description());
    println!("{}", movie.description());
    println!("{}", audiobook.description());
}

```

The method above appears to be very verbose and unnatural when checking the variant, but this syntax is also used in other places, so we can familiarize ourselves with it here first.

Usually, when implementing an enum, using match is more concise.

**match**

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
    //  ^^^^^^^^^^ to check which variant we are working with
            Media::Book { title, authour } => {
        //  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ it means if self is a `Media::Book`, put the `title` and `author` to be used in the block
                format!("{} by {}", title, authour)
            },
            Media::Movie { title, director } => {
                format!("{} by {}", title, director)
            },
            Media::Audiobook { title } => {
                format!("{} by {}", title, "unknown")
            }
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

    println!("{}", book.description());
    println!("{}", movie.description());
    println!("{}", audiobook.description());
}
```

Because there is only one line of expression within the curly brackets, it can be further simplified as follows:

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
            //                                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ no curly brackets now, just the expression
            //                                                                     but don't lose end of ',' after the expression
            Media::Movie { title, director } => format!("{} by {}", title, director),
            Media::Audiobook { title } => format!("{} by {}", title, "unknown"),
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

    println!("{}", book.description());
    println!("{}", movie.description());
    println!("{}", audiobook.description());
}
```

Although we need to check which variant of the enum self is when implementing methods on enum, using match syntax can still keep our code concise.
```rust
