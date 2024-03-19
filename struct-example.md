---
source: https://doc.rust-lang.org/book/ch05-02-example-structs.html
tags:
  - rust
  - draft
---
implementing a rectangle area computing program.

```rust
fn area(width: u32, height: u32) -> u32 {
  width * height
}
```

`width` and `height` are not grouped together. no where does the program explicitly express or enforce the relationships between these two values

```rust
fn area(dimensions: (u32, u32)) -> u32 {
  dimensions.0 * dimensions.1
}
```

using a tuple does group the values together however which of the values is `width` and which is `height` is determined implicitly by the convention of positions. accessing the tuple elements with the `dimensions.0` or `dimensions.1` does not explicitly convey which dimension is being accessed.

```rust
struct Rectangle {
  width: u32, 
  height: u32,
}

fn area(rect: Rectangle) -> u32 {
  rect.width * rect.height
}
```

using structs addresses these issues.

> [!note]
> - since the `Rectangle`  struct does not implement the `Copy` trait and `area` takes ownership of the `rect` argument which is rather inconvenient
> - this can be addressed by deriving the `Copy` trait on `Rectangle` using `#[derive(Copy)]`
> - or by changing the signature of area to accept references `fn area(rect: &Rectangle) -> u32`

`std::fmt::Display` trait produces output intended for direct user consumption. It is automatically implemented for types that have only one obvious way they could possibly be displayed i.e. most primitive types, `String`.

`println!("{}", x)` uses the `Display` trait of x.

`std::fmt::Debug`trait produces output that is useful for debugging. 
`println!("{:?}", x)` uses the `Debug` trait of x.

`#[derive(Debug)]` can be placed on top of a struct to automatically implement the `Debug` trait for said struct


the `dbg!(value)` takes ownership of `value`, prints the file and line number and returns ownership of the resulting value.
