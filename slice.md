---
source: https://doc.rust-lang.org/book/ch04-03-slices.html
tags:
  - rust
  - draft
---
a slice is a reference to a contiguous sequence of elements in a collection.

```rust
let x: String = String::from("hello world");
let hello: &str = &x[0..5];
let world: &str = &x[6..11];
let hello_world: &str = &x[..];
```

```rust
let x: String = String::from("Lorem Ipsum");
let lorem: &str = &x[..5];
let ipsum: &str = &x[6..];
```

the string slice `&str` stores a pointer to the start of the slice and the length if the slice of the string being referenced. this data is of course fixed in length (`2 * usize`) and stored on the stack

![[Pasted image 20240318122205.png]]

> [!warning]
> string slice ranges must occur at valid UTF-8 character boundaries

> [!note]
> Rust's range syntax `start..end` used in `for` and slices
> - probably some sort of range object itself
> - ends at `end - 1`

```rust
fn first_word(s: &String) -> &str {
    for (i, char) in s.bytes().enumerate() {
        if char == b' ' { return &s[0..i] } 
    }

    &s[..] 
}
```

note that the complier ensures that the slices references returned are always valid 

```rust
fn main(){
    let x: String = String::from("Lorem Ipsum");

    // word is a reference to x
    // if x is dropped the word reference becomes dangling
    let word: &str = first_word(&x); // word scope starts here

    println!("{}", word); // word scope ends here

    // since word refence is "out of scope" x can be moved
    take_ownership(x); // x dropped here
}
```

```rust
fn main(){
    let x: String = String::from("Lorem Ipsum");

    // word is a reference to x
    // if x is dropped the word reference becomes dangling
    let word: &str = first_word(&x); // word scope starts here

    
    // compiler error: can not move out of a borrowed value
    // since the word reference is still valid when x is moved
    // compiler throws an error
    take_ownership(x); // x dropped here

    println!("{}", word); // word scope ends here
}
```

```rust
fn main(){
    let mut x: String = String::from("Lorem Ipsum");

    // word is a reference to x
    // if x is dropped the word reference becomes dangling
    let word: &str = first_word(&x); // word scope starts here

    // clear takes a mutable reference to x
    // mutable references must be the sole reference
    // but the immutable refence word exists
    // compiler error: can not borrow x as mutable because it is already borrowed as mutable
    x.clear();

    println!("{}", word); // word scope ends here
}
```

***string literals***
string literals are hardcoded into the program binary 
string literals are slices which point to the location in the binary where the string is hardcoded
`s` stores a pointer and the length of the string (in this case `11`).
```rust
let s: &str = "hello world";
```

altering the signature of `first_word` to accept string slices instead of `String` references allows the function to accept but string slices and `String` references.

this is due to implicit deferencing provided by rust 

```rust
fn first_word(s: &str) -> &str {
  // implementation
}
```

```rust
fn main(){
    let x: String = String::from("Lorem Ipsum Idor");
    println!("{}", first_word(&x)); // rust automatically converts &String to &str
    println!("{}", first_word(&x[6..]));
    println!("{}", first_word("hello world"));
}
```

the slice type is general 

```rust
let arr: [u8; 5] = [10; 5];
let slc: &[u8] = &arr[0..2];
```