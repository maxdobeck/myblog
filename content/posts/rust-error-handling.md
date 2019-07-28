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

The goal with Result is to handle errors gracefully.  Unlike the "Try, Catch, Finally" pattern we're not going to say, "hey attempt this and if it blows up handle it this way, this way, or this way." Not that there's anything wrong with that &#128522;.

### non-rust pseudo code example

```
user_input = get_STDIN()

try () {
	do something with user_input on the happy_path()
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

### src/rust-example with [match](https://doc.rust-lang.org/rust-by-example/flow_control/match.html)
```
use std::process;

fn main() {
	// we're getting the 1st arg in a list of n-long args here
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
compiles &#x2705;

Some and None for the non-rustacean -> https://stackoverflow.com/questions/24771655/some-and-none-what-are-they

Important part quoted here because links die:

>None and Some are the variants of the enum, that is, a value with type Option<T> can either be a None, or it can be a Some containing a value of type T.

>This is the same as the core data Maybe a = Nothing | Just a type in Haskell; both represent an optional value, it's either there (Some/Just), or it's not (None/Nothing).

>These types are often used to represent failure when there's only one possibility for why something failed, for example, .get uses Option to give type-safe bounds-checked array access: it returns None (i.e. no data) when the index is out of bounds, otherwise it returns a Some containing the requested pointer.

--[Shepmaster](https://stackoverflow.com/users/155423/shepmaster)

