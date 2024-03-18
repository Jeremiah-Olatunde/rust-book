---
source: https://doc.rust-lang.org/book/ch03-03-how-functions-work.html
tags:
  - rust
  - draft
---
***functions***
defined with the `fn` keyword  
rust function use `snake_case` by convention. 
the location of a functions definition does not matter, all that matters is that the definition is in the scope of the invocation.

parameters are defined in a comma separated list. each parameter must have the name and a type annotation.  
function bodies in rust consist of a series of statements followed by an optional expression. the result of which is implicitly returned.  
the `return` keyword is used to explicitly return from a function.
a function which lacks an ending expression and an explicit return automatically returns the unit type `()`.

```rust
fn add_u8(x: u8, y: u8) -> u8 {
    x + y
}

fn main(){
    let result = add_u8(5, 10);
    println!("{result}");
}
```

```rust
fn say_hello() -> () {
    println!("hello world");
}

fn main(){
    say_hello();
}
```

```rust
fn add_u8(x: u8, y: u8) -> u8 {
    x + y; // expression turned into stateement with semicolon
    // implicitly returns ()
    // conlfict with u8 annotation
    // compiler error
}

fn main(){
    let result = add_u8(5, 10);
    println!("{result}");
}
```


***statements and expressions***
- statements perform an actions and do not return a value
  - variable or function declarations and assignments `let`, `fn`
- expressions represent some computation that results in a value
  - math expressions (`5 + 6`), values, `10`, `"A"`, function calls, macro calls, block scope
  - 
> [!note]
> in JavaScript variable assignments are expressions and return the value assigned to the variable this is not the case in Rust

> [!note]
> adding a `;` to the end of an expression turns it into a statement

block scopes are a series of statements followed by an optional expression. the result of the expression is returned by the block. in this sense a function is just a named block which accepts parameters.

```rust
fn add_u8(x: u8, y: u8) -> u8 {
    x + y
}

fn main(){
    
    let result  = {
        let x = 10;
        let y = 20;
        add_u8(x, y)
    };
    
    println!("{result}");
}
```

