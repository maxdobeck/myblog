---
title: "Rust Error Handling"
date: 2019-07-14T21:26:34-07:00
draft: true
---

# Handling Errors in Rust
To start you have to know about Rust's Enums (Enumerables).  Essentially you can think of erros as a return statement that could be one of two things: ERROR or OK.  This is called the `Result` enum (enumerable).  

If a function succeeds you get OK.
If a function fails you get ERROR and some details bubbled up from the error. 

Rust By Example is fairly dry and but covers this well: https://doc.rust-lang.org/rust-by-example/std/result.html
Actual documentation for reference: https://doc.rust-lang.org/std/result/enum.Result.html

The goal with Result is to handle errors gracefully.  Unlike the Try, Catch, Finally pattern we're not going to say, "hey attempt this and if it blows up handle it this way, this way, or this way." 




