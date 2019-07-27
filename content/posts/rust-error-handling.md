---
title: "Rust Error Handling"
date: 2019-07-14T21:26:34-07:00
draft: true
---

# Handling Errors in Rust
To start you have to know about Rust's Enums (Enumerables).  Essentially you can think of errors as a return statement that could be one of two things: ERROR or OK.  This is called the `Result` enum (enumerable).  

If a function succeeds you get OK.
If a function fails you get ERROR and some details bubbled up from the error. [Rust By Example](https://doc.rust-lang.org/rust-by-example/std/result.html) is fairly dry but covers this well: 
Actual documentation for reference: https://doc.rust-lang.org/std/result/enum.Result.html

The goal with Result is to handle errors gracefully.  Unlike the "Try, Catch, Finally" pattern we're not going to say, "hey attempt this and if it blows up handle it this way, this way, or this way." Not that there's anything wrong with that 👍.

I.e. instead of 
```
// non-rust pseudo code example
user_input = get_STDIN()

try () {
	do something with user_input
except as IO_err:
    do something because of this specfic problem
except as no_input_err:
    do something because of this specfic problem
except:
    do something because of this generic problem
finally:
    do something else
}
```

```
// src/rust-example
use std::process;

fn main() {
    let path = match std::env::args().nth(1) {
        Some(arg) => arg,
        None => {
            println!("No args found");
            process::exit(1);
        },
    };

    println!("{}", path)
}
```
compiles ✔️

While we achieve the same thing  