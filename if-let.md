---
source: https://doc.rust-lang.org/book/ch06-03-if-let.html
tags:
  - rust
  - draft
---
`if let` is syntatic sugar for a `match` statement the runs code when the value matches a specific pattern and ignores or performs a general action all other cases.

```rust
fn main(){
    let value = Some(4);

    // verbose match equivalent
    match value {
      Some(5) => println!("woohoo 5"),
      _ => (), // ignore all other cases
    }

    if let Some(5) = value { println!("woohoo 5") }; 
    // no else statement ignores all other cases

    if let Some(5) = value { println!("woohoo 5") }
    else { println!("boohoo not 5") }  // perform general action in all other cases
}
```

```rust
enum MyEnum {
    Foo(u8),
    Bar(u8),
    Baz(u8)
}

fn main(){
    let value = MyEnum::Foo(25);

    if let MyEnum::Foo(x) = value {
        println!("Fooing {} times", x)
    };
}
```

in a sense `if let` combines the convenience of not having exhaustiveness checking with the safety of having it.  but complete logical correctness is given up for program correctness.

the program will never crash because a case wasn't handled and it is not aware of how to move forward. 

you either do *"x"* for the one specific case or *"y"* for all other cases. the program always knows what to do; either *x* or *y*.

but you might forget to handle an case that might be important to handle in the business logic sense and it falls into the catch all case leading to incorrectness.

`if let` is an expression meaning it returns a value. usually it returns `()` the unit type.
if a value other than the unit type is returned an `else`  block must be specified and must return a value of the same type as the `if let` block

this preserves the program correctness guarantees of rust.  

if the rule wasn't in place and rust instead returned `()` for all non matching cases and a value of type `T` for the specified matching case then that is essentially the billion dollar `null` mistake




