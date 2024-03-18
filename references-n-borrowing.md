---
source: https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html
tags:
  - rust
  - draft
---
a reference stores the memory address of the data is refers to.  
references are always guaranteed to be valid unlike pointers.
references allow values to be "passed" around without transferring ownership of the value
the action of creating a reference is referred (pun intended) to as *borrowing*


```rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);
    
    // s1 is still valid here as it was not moved into s
    println!("The length of '{}' is {}.", s1, len);
} // s1 goes out of scope here and String::drop is called

fn calculate_length(s: &String) -> usize {
    // note that s is automatically dereferenced
    s.len()
} // s goes out of scope here and the pointer is popped of the stack
```

![[Pasted image 20240318052833.png]]


> [!note]
> `s1` is the `String` type itself not the data on the heap that `ptr` points to.  
> hence when a `String` type goes out of scope `String::drop` is called.
> `s` is a pointer, a value stored on the stack, hence it is just popped of the stack.

references like variables are immutable by default and must be marked with `mut` to be mutated 

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

mutable references must always be the sole reference (mutable or immutable) to the values they reference.

```rust
let mut s = String::from("hello");

let r1 = &mut s;
// cannot borrow `s` as mutable more than once at a time
let r2 = &mut s;

println!("{}, {}", r1, r2);
```

this behavior prevents data races 
[ read-later :: https://en.wikipedia.org/wiki/Race_condition]
[ read-later :: https://en.wikipedia.org/wiki/Linearizability]

new references can be created when previous ones go out of scope.
a references scope starts from where it is introduced to the last time it is used (*not the end of the current scope*)

```rust
let mut s = String::from("hello");

{
  let r1 = &mut s;
} // r1 goes out of scope here, so we can make a new reference with no problems.

let r2 = &mut s;
```

```rust
let mut s = String::from("hello");

let r1 = &mut s; // r1 scope starts and ends here

let r2 = &mut s; // r2 scope starts here

println!("{}", r2); // and ends here

// hence another &mut can be taken
let r3 = &mut s;
println!("{}", r3);

```

***dangling references***
a dangling pointer is one that points to a location in memory which has been reallocated. the compiler prevents dangling references but ensuring the referenced data does not go out of scope before the reference does. (recall that data is cleaned up and deallocated once its owner goes out of scope). This is accomplished using lifetimes.

```rust
fn dangle() -> &String { // dangle returns a reference to a String

    let s = String::from("hello"); // s is a new String

    &s // we return a reference to the String, s
} // Here, s goes out of scope, and is dropped. Its memory goes away.
  // Danger!
```

