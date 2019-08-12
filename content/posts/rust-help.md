---
title: "Rust Help"
date: 2019-08-01T22:31:17-07:00
tags:
  - rust
---

# Rust Help

### Dependencies 
You have a crate you want to use!  Cool!  Open `Cargo.toml` and add it to the Dependencies section!

The format is `official-crate-name = "ver.number.you.want"`.  Usually the version number is listed out on [crates.io](https://crates.io/).

For example:
```
[dependencies]
failure = "0.1.5"
```

However lets say there's a hiccup. You have a crate you want to use but the maintainers have changed the API so much that the next version isn't going to be fully backwards compatible!  Instead of using the latest public version you want to use the Master branch from the github repo.

Boom, specify the repo and the branch:

```
[dependencies]
cpal = { git = "https://github.com/tomaka/cpal", branch = "master" }
failure = "0.1.5"
```

This is also helpful if you want to use a specific branch for testing.  Say you're working on a feature branch and need to verify everything compiles.  You can specify it in the Cargo.toml file AND you can use this change in you CICD system.  You'll know asap when something doesn't work.

Read more here:
https://github.com/rust-lang/cargo/blob/master/src/doc/src/reference/specifying-dependencies.md

### Examples
Lets say you have a library you're working on or you're just testing.  How do you compile, test, and execute code without a `main.rs` file?

1. Add an `examples` folder
1. `$ ls`
1. Cargo.lock  Cargo.toml  `examples`  src  target
1. Add one-off file likes test1.rs test2.rs to `examples`

Voila you're done!  Just compile or build like so:
`cargo run --example test1` or `cargo build --example test1`
- notice I dropped the `.rs` 
- notice the --example flag is singular
- --examples will error

	