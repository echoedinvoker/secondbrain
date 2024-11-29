---
date: 2024-11-15
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# When to use which ways to handle Result value

In [[2024-11-15_Write-error-lines-to-txt-file|this example]] we know there are three ways to handle the Result value, we will compare when to use which way in this topic.

| # | approach | situation |
|---|------------|--------------|
| 1 | Use a match or 'if let' statement | When you're ready to meaningfully deal with an error |
| 2 | Call 'unwrap()' or 'expect()' on the Result | Quick debugging, or if you want to crash on an Err() |
| 3 | Use the try operator ('?') to unwrap or propagate the Result | When you don't have any way to handle the error in the current function |


## 1. Use a match or 'if let' statement

When we have a clear way to handle Err, it is recommended to use match or if let to handle Result.

```rust
use std::io::Error;
use std::fs;

fn read_config_file() -> Result<String, Error> {
    fs::read_to_string("config.txt")
}

fn get_config() -> String {
    let default_config = String::from("{ enable_debug: false, enable_logging: false }");  // backup config

    match read_config_file() {  // We're ready to handle the error with backup config, so we use `match`
        Ok(config) => config,
        Err(_) => {
            eprintln!("Failed to read config file, using default config");
            default_config  // When Err, return default config
        }
    }
}

fn main() {
    let config = get_config();

    println!("Config: {}", config);
}

```


## 2. Call 'unwrap()' or 'expect()' on the Result

This is a method of directly crashing the app, which we should rarely use outside of debugging situations.


## 3. Use the try operator ('?') to unwrap or propagate the Result

This way is the opposite of the first option. When we have no way to handle errors or when there is nothing else to do when Err occurs, we can use this method.

```rust
use std::fs;
use std::io::Error;

fn read_config_file() -> Result<String, Error> {
    fs::read_to_string("config.txt")
}

fn get_config() -> Result<String, Error> {
//              ^^^^^^^^^^^^^^^^^^^^^^^^ because we want to use the `?` operator this scope

    let config = read_config_file()?;
    //                             ^  because we have nothing to do with the error,
    //                                `?` will retrun an early Error to parent and
    //                                hope parent will handle it.

    Ok(config) // because we defined retrun type is Result<String, Error>
               // so we have to return Result type value here.
}

fn main() -> Result<(), Error> {
//        ^^^^^^^^^^^^^^^^^^^^ because we want to use the `?` operator this scope
    
    let config = get_config()?;
    //           ^^^^^^^^^^^^^ if get_config() failed, its `?` will return early Error
    //                         to here, and we have to handle it.
    //                         Now we just use `?` again, which means this scope has
    //                         nothing to do with that Error either...
    //                         because here is already the main scope, so the Error return
    //                         here will be printed to console by default.

    println!("Config: {}", config);

    Ok(()) // because we defined retrun type is Result<(), Error>
           // so we have to return Result type value here.
}

```

As shown above, the try operator passes the Error to the upper layer until someone can handle the Error. If no one handles the Error until the main function, it will be printed to the console by default.

