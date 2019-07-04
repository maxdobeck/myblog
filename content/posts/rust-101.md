---
title: "Rust 101"
date: 2019-06-30T17:17:37-07:00
draft: true
---

# Rust: echoing user input
This is a straight forward program that spits out whatever the input is. User is asked to give input, user types in some string, hits enter, and it is printed out.

```
Enter your text:hello
hello
```

We're going to cover:

* getting input
```
.read_line(&mut val)
```

* Variables in Rust
```
let mut val = "I'm a string".to_string();
```

* returning values from a function
```
fn get_input() -> String
```

## Final Product
This is the whole [echo](https://github.com/maxdobeck/echo) program in main.rs.

```
// src/main.rs
use std::io;
use std::io::Write;

fn main() {
    let input = get_input();
    println!("{}", input);
}

fn get_input() -> String {
    print!("Enter your text: ");
    io::stdout().flush().unwrap();
    let mut val = String::new();

    io::stdin()
        .read_line(&mut val)
        .expect("Error getting input");

    return val;
}
```

The way variables are declared may seem foreign if you're unfamiliar with C/C++ or languages.  Usually when you get input in a language, like Python, you'd just say something like, `user_input = raw_input()`.  You ask for input, assign it to some variable and you're done!

At this point in Python you could re-use `user_input` and say, `user_input = 100` or `user_input = "Hello World"`. If you did the same in Rust you'd end up fighting against the Type system and the default immutability of variables.  

With Rust's Type System if you said, `user_input = 100` you'd get an error like, "user_input is not of type String" or "String cannot be an Integer".  The violation stems from the foundation of Rust; each variable must have a Type (string, integer, etc). This way the compiler can check to see if you're assigning an incorrect value to a variable.  And you can build functions around certain Types.

With the default immutability of variables if you tried `user_input = "Hello world"` you'd encounter an error like, "`user_input was not declared as mut[mutable]`"(emphasis mine). This prevents you, another programmer, or another piece of the program from mistakenly referring to a variable as if it couldn't change.  Rust of course does let you define mutable variables that can be changed.
 
## Variables in Rust
It is important to remember that variable mutability is not purely a performance issue.  We are not really doing this to make sure each byte of RAM or each CPU cycle is used to maximum efficiency.  This is about leveraging the compiler to catch human errors.

If you can compile your code and let the computer error when you try to change a value that should be static, then you can free up your time and energy focusing on the design of the program.  The benefits of this initial time investment usually pay off when you're looking for performance or memory safety.  Usually asynchronous programming is the area that helps the programmer the most.

We'll use the `print!("Enter your text: ");` prompt string as an example.  Lets rewrite it to be mutable then immutable, to see how different the code looks.

##### Inferred Mutable Variable
```
let mut prompt = "Enter your text:".to_string();
prompt = "What do you want to echo?".to_string();
print!("{}", prompt);
```
Here we infer the TYPE i.e. of a mutable variable. The compiler guesses that `prompt` should be a String and not something else like an Integer or Float.

This part of the program will complile!  So we're legally overwriting the contents of `prompt`.  The compiler will generate a warning about prompt's value not being read though but we can ignore that for now.

#### Annotated Immutable Variable

```
let prompt: String;
prompt = "Enter your text:".to_string();
print!("{}", prompt);
```
In this case we annotated the variable `prompt` with the keyword String.  If we put `let prompt: i32;` this would prevent a string or set of characters from being used.  In fact the only value could be of type Integer, in our case a 32-bit integer.  Rust lets you get fairly granual with integers to be flexible.

In this example we cannot change prompt.  For all time prompt will be contain "Enter your text:".  Unless we shadow it. Shadowing is when you re-use the name with the `let` keyword.  This will destroy the original variable prompt but that can be a good thing.


#### Shadowing Immutable Variables
This will compile and output the number 42.

```
let prompt: String = "Enter your text: ".to_string();
let prompt = 42;
print!("{}", prompt);
```

As you can see it looks like we've violated the Type system but no we're just destroying the original prompt var, creating a new one, and thus annotating a new type to a new variable.  No mismatch between the variable Type and its contents!

## Getting Input from the user
Relative to other languages its hard to get text from a user.  In total it takes about 7 lines if you leave those 2 method calls formatted in line with the preferred Rust Format.

```
use std::io;
use std::io::Write;
io::stdout().flush().unwrap();
let mut val = String::new();

io::stdin()
    .read_line(&mut val)
    .expect("Error getting input");
```

Here's a concise list of what we're doing:
1. Importing (bringing into scope of main.rs) the libraries for Input/Output.
use std::io;
use std::io::Write;

2. Flushing the stdout() for reasons we'll ignore.
io::stdout().flush().unwrap();

3. Creating a String variable, which we're now experts on!
let mut val = String::new();

4. Referencing the IO libary's stdin() Function which then has a trait read_line() that needs a String variable to store the input.  We'll ignore .expect() but basically its for catching Errors.  If there were an error at this point we'd see "Erorr getting input" from the program. 
io::stdin()
    .read_line(&mut val)
    .expect("Error getting input");

Some would call this overly verbose 