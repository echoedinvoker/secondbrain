---
date: 2024-11-11
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Model several different things that are all kind of similar by enum

```rust
#[derive(Debug)]
struct Book {
    title: String,
    authour: String,
}

#[derive(Debug)]
struct Movie {
    title: String,
    director: String,
}

#[derive(Debug)]
struct Audiobook {
    title: String,
}

fn print_book(book: Book) {
    println!("{:?}", book);
}

fn print_movie(movie: Movie) {
    println!("{:?}", movie);
}

fn print_audiobook(audiobook: Audiobook) {
    println!("{:?}", audiobook);
}

fn main() {
    let book = Book {
        title: String::from("The Catcher in the Rye"),
        authour: String::from("J.D. Salinger"),
    };

    let movie = Movie {
        title: String::from("Inception"),
        director: String::from("Christopher Nolan"),
    };

    let audiobook = Audiobook {
        title: String::from("The Great Gatsby"),
    };

    print_book(book);
    // Book { title: "The Catcher in the Rye", authour: "J.D. Salinger" }
    print_movie(movie);
    // Movie { title: "Inception", director: "Christopher Nolan" }
    print_audiobook(audiobook);
    // Audiobook { title: "The Great Gatsby" }
}

```
We found that struct Book, Movie, Audiobook all have the title attribute, and they all belong to media. We can use `enum` to simplify the code as follows:

```rust
#[derive(Debug)]
enum Media {
//   ^^^^^ Then, Rust will treat `Media` as a type. It means function and data structure can accept `Media` value
    Book { title: String, authour: String },
    //   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ different from enum of other languages, Rust enum item can have data structure
    //                                      You can think it just like structs in enum, but there is no any struct, in fact
    Movie { title: String, director: String },
    Audiobook { title: String },
}

fn print_media(media: Media) {
//                    ^^^^^ Now, we don't need to write three function for Book, Movie, Audiobook
//                          We can use only one function to handle all of them with type `Media`
    println!("{:?}", media);
}

fn main() {
    let book = Media::Book {
    //         ^^^^^^^^^^^ this is how to create an instance of enum variant

    //  and like struct, we initialize it to an instance of the enum variant
        title: String::from("The Catcher in the Rye"),
        authour: String::from("J.D. Salinger"),
    };

    print_media(book);
    //          ^^^^ no matter which variant of enum, their type is `Media`, so we can use only one function to handle all of them
}

```

In Rust, you can find that enum are treated as type, so they can generate instances and be accepted by functions or [[2024-11-11_Example-to-store-enum-in-fields-of-structs|store enum in fields of struct instances]]. This greatly enhances the utility of enums in Rust compared to other languages.

[[2024-11-11_Deciding-when-to-use-enums-vs-structs-when-there-are-many-things-need-to-define|here]] is a more precise way to determine whether to use enums or structs when we need to define many things. 
