---
source: https://doc.rust-lang.org/book/ch05-03-method-syntax.html
tags:
  - rust
  - draft
---
methods are functions defined within the context of a struct, enum or trait object.  
the first parameter is always `self` which represents the instance the method is being called on.

methods are defined with an implementation block. methods defined within an implementation block are called associated functions as they are associated with the type for which they are being implemented.

```rust
impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }
}
```

`Self` is a type alias for the type for which the implementation block belongs.
`self` is short for `self: Self` and takes ownership of the instance on which the method was called
`&self` is short  for `self: &Self` and takes a immutable reference of the instance
`&mut self` is short for `self: &mut Self` and takes a mutable reference

methods can be called on instances using the method syntax

```rust
let rect = Rectangle {
    width: 5, 
    height: 5,
};

println!("{:?}", rect.area());
```

methods can have the same identifiers are fields without causing conflict. By convention same named methods are used to implement getter or setter for the fields after which they are named.


> [!note] Pointers vs References
> the distinction between pointers and references in Rust. Pointers are just memory addresses while references are memory addresses that are guaranteed to point to a valid value.  
> references use `&`  while pointers use `*`

> [!note] Automatic Deferencing
> the `obj -> method()` operator is used in C/C++ to automatically deference a pointer to an object and invoke `func` on that object. it is identical to `(*obj).method()`.  
> rust uses automatic deferencing instead.  
> the compiler automatically inserts `&ptr` or `&mut ptr` so that the call matches the signature of the method  
> hence why `rect.area()` is equivalent to `&rect.area()`


functions that are associated with a type but not methods omit `self` in their type signature. these are typically used for constructors that return a new instance of the type: `String::new()`, `Vec::new()`.

the `::` syntax is used to access them. `::` is used for namespacing. the function is namespaced by the struct

```rust 
impl Rectangle {
    fn square(l: u32) -> Rectangle {
        Rectangle {
            width: l,
            height: l
        }
    }
}

fn main(){
    println!("{:?}", Rectangle::square(5));
}
```

there can be multiple `impl` blocks for one type