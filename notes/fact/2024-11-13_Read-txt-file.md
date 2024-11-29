---
date: 2024-11-13
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Read txt file


## Requirement

```bash
~/D/g/s/r/logs > cat logs.txt 
INFO 14:30:25 Application started.
WARNING 14:32:10 Missing configuration defaults to 'standard mode'.
ERROR 14:33:45 Failed to connect to the database.
INFO 14:35:20 User logged in successfully.
INFO 14:36:15 Data fetch operation completed.
WARNING 14:37:50 Unable to find user profile, using guest settings.
ERROR 14:39:05 Could not write to configuration file.
INFO 14:40:55 Backup process initiated.
WARNING 14:42:30 Low disk space on the backup drive.
ERROR 14:44:15 Backup failed due to insufficient space.
INFO 14:45:35 Scheduled task executed.
WARNING 14:47:12 Configuration file not found, using default configuration.
ERROR 14:48:58 Unable to access remote server.
INFO 14:50:25 User account updated.
ERROR 14:51:40 Failed to process transaction ID: 2345.
INFO 14:53:15 Application shutdown initiated.
WARNING 14:54:42 Failed to stop background services.
ERROR 14:56:20 Unhandled exception in main thread.
INFO 14:58:05 System reboot scheduled.
INFO 14:59:30 Application restarted.
INFO 15:01:00 Network connectivity restored.⏎   
```

Above is a txt file that we have prepared, we hope to read this file in a rust app.


## std::fs::read_to_string to read file

We can simply use `std::fs::read_to_string` to read the file content.

```rust
// src/main.rs

use std::fs;  // use the fs module (fs = file system) to read the file
              // std module (standard library) is always included by default
              // here we just use `use` to simplify the code, not include

fn main() {
    let text = fs::read_to_string("logs.txt");
    //         ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ simply use the method out of the fs module to read the file

    println!("{:#?}", text); // this is bad practice, we will fix this line later
}

```

```bash
~/D/g/s/r/logs > cargo run -q
Ok(  # Ok is very similar to enum `Option` logic, it is used to do a bit error handling here
    "INFO 14:30:25 Application started.\nWARNING 14:32:10 Missing configuration defaults to 'stand
ard mode'.\nERROR 14:33:45 Failed to connect to the database.\nINFO 14:35:20 User logged in succes
sfully.\nINFO 14:36:15 Data fetch operation completed.\nWARNING 14:37:50 Unable to find user profi
le, using guest settings.\nERROR 14:39:05 Could not write to configuration file.\nINFO 14:40:55 Ba
ckup process initiated.\nWARNING 14:42:30 Low disk space on the backup drive.\nERROR 14:44:15 Back
up failed due to insufficient space.\nINFO 14:45:35 Scheduled task executed.\nWARNING 14:47:12 Con
figuration file not found, using default configuration.\nERROR 14:48:58 Unable to access remote se
rver.\nINFO 14:50:25 User account updated.\nERROR 14:51:40 Failed to process transaction ID: 2345.
\nINFO 14:53:15 Application shutdown initiated.\nWARNING 14:54:42 Failed to stop background servic
es.\nERROR 14:56:20 Unhandled exception in main thread.\nINFO 14:58:05 System reboot scheduled.\nI
NFO 14:59:30 Application restarted.\nINFO 15:01:00 Network connectivity restored.",
)

```


## Use `match` to handle return `Result` value from `fs::read_to_string`

Because `fs::read_to_string` returns a `Result`, we cannot manipulate the actual content obtained. We must use `match` or `let if` to unwrap this value so that we can truly obtain the content.

If you are not familiar with enum `Result`, you can check [[2024-11-13_Example-to-implement-error-handling-on-divide-function|this topic]].

```rust
use std::fs;

fn main() {
    match fs::read_to_string("logs.txt") {
//  ^^^^^ use `match` to handle all possible cases of Result type value
        Ok(content) => println!("{}", content.len()),
        // ^^^^^^^                    ^^^^^^^^^^^^^ here we got unwrapped content, we can really use it now

        Err(error) => println!("Error: {}", error), // if error, print the error message, Result type forces us to handle all possible cases
    }
}

```

Not only `std::fs::read_to_string`, many methods will return `Result` value especially related to IO, we must get used to handling it in the correct way above.
