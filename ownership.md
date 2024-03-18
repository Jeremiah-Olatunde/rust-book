---
source: https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html
tags:
  - rust
  - draft
---
***the stack***
- last in, first out
- basic operations are the push and pop
- data on the stack must have a known fixed size

[ read-later :: https://en.wikipedia.org/wiki/Stack_(abstract_data_type)]

***the heap***
- a portion of main memory used for the heap
- allocation
  - allocator finds a sufficiently large portion of the heap
  - marks it as being used 
  - returns a pointer  to that location in memory.
- deallocation
  - the allocated memory referenced by the pointer is marked as free
  - the pointer is stored on the stack as it is a fixed length memory address
  - the stored data there is usually not erased


how data on the heap is managed depends on the language in question. Solutions range from garbage collection, manual memory management (`malloc`, `free`) to the system of ownership used by rust


programs work primarily with the data on the stack. 
data in the heap is accessed by following pointers on the stack.
when a function is called the function data (arguments, local variables) are pushed onto the stack.
they are popped of when the function returns.

the heap is used for data of unknown compile time size i.e. a dynamic run time size.
the overhead required for the allocation and deallocation makes the heap slower than the stack. 

values stored on the stack can be duplicated with little to no overhead.

[ read-later :: https://en.wikipedia.org/wiki/Memory_management#HEAP]

***ownership***

rules
- each value has an owner
- only one owner at a time
- value is dropped when its owner goes out of scope

a variable is valid from the point it is declared up until the end of the scope in which it is declared.


```rust
fn main(){
  {
    // "hello world" is a string literal
    // it is of a fixed and known length at compile time
    // hence is stored on the stack
    let s = "hello world"; // variable s is valid from here...
    // ..
    // ..
  } // till the end of the scope here
}
```


`String` string data on the heap and as such is able to store large amounts of dynamically sized strings.
- `String` constructors allocate a memory on the heap
- stores the data in the location on the heap
- returns a pointer to the location of the data on the heap

when the owner of `String` goes out of scope the value is *dropped* and the `drop` function on `String` handles the process of deallocating the memory

```rust
{
  let s = String::from("hello world"); // s is the owner
} // s goes out of scope and string is dropped
```

integers are have a known fixed size and are stored on the stack. Due to this when `x` is copied to `y` the value (`5`) is duplicated. 
the stack contains `5` and `5`

> [!question]
> what do `x` and `y` really store
> - pointers to the stack addresses of the values
> - are they merely abstractions in that they do not *exist* 
>   - *compiled* away
>   - would `x + y` pop the values (`5`) of the stack, into registers and the pop the result back on to the stack

```rust
let x: u8 = 5;
let y: u8 = x;
let z: u8 = x + y;
```

The behavior differs when values stored on the heap are copied.

```rust
{
  let s1 = String::from("hello world");
  let s2 = s0;
}
```

a `String` (`s1`, `s2`) consists of 3 parts
- length of the string in bytes
- the capacity i.e. the maximum string length that can be stored in the currently allocated space
- a pointer to the location on the heap where the actual string data is

this collection of data is of a fixed size (probably 3 * `usize`) and is stored on the stack

![[Pasted image 20240318045148.png]]

when `s1` is copied to `s2` it is this data that is duplicated.
as it is purely stack data the duplication is inexpensive.
however this action has the caveat of transferring ownership from `s1` to `s2` invalidating `s1`.
this process is called a ***move***

![[Pasted image 20240318045838.png]]

now the data on the heap is cleaned up when `s2` goes out of scope as it is the new owner.
meanwhile `s1` can no longer be used.

```rust
  let s1 = String::from("hello");
  let s2 = s1;

  // compile time error
  println!("{}, world!", s1);
```

deep copies of data are never automatically created. All automatic copies can be assumed to be inexpensive in terms of runtime performance. 

> [!note]
> the `String::clone` method can be used to create explicit true deep copied of heap data

note that when `x` was copied into `y`, `x` was not invalidated unlike with the `String` scenario. This is due to the fact that when stack data is copied true duplicates are made, as such it is completely *"safe"* to continue using `x`.

the `Copy` trait is place on types that are stored entirely on the stack.  
it indicates to the compiler when variables are trivially duplicated or moved when an assignment to another variable occurs.

the compiler prevents types implementing the `Drop` trait from implementing `Copy`. if a type requires memory to be cleaned up after it goes out of scope (i.e. it stores data on the heap) then it can not be trivially copied

***ownership and functions***
the mechanics described above hold when variables are passed into functions

```rust
fn main() {
    let s = String::from("hello");  // s comes into scope

    takes_ownership(s);             // s's value moves into the function...
                                    // ... and so is no longer valid here

    let x = 5;                      // x comes into scope

    makes_copy(x);                  // x would move into the function,
                                    // but i32 is Copy, so it's okay to still
                                    // use x afterward

} // Here, x goes out of scope, then s. But because s's value was moved, nothing
  // special happens.

fn takes_ownership(some_string: String) { // some_string comes into scope
    println!("{}", some_string);
} // Here, some_string goes out of scope and `drop` is called. The backing
  // memory is freed.

fn makes_copy(some_integer: i32) { // some_integer comes into scope
    println!("{}", some_integer);
} // Here, some_integer goes out of scope. Nothing special happens.
```

and when returning values from functions

```rust
fn main() {
    let s1 = gives_ownership();         // gives_ownership moves its return
                                        // value into s1

    let s2 = String::from("hello");     // s2 comes into scope

    let s3 = takes_and_gives_back(s2);  // s2 is moved into
                                        // takes_and_gives_back, which also
                                        // moves its return value into s3
} // Here, s3 goes out of scope and is dropped. s2 was moved, so nothing
  // happens. s1 goes out of scope and is dropped.

fn gives_ownership() -> String {             // gives_ownership will move its
                                             // return value into the function
                                             // that calls it

    let some_string = String::from("yours"); // some_string comes into scope

    some_string                              // some_string is returned and
                                             // moves out to the calling
                                             // function
}

// This function takes a String and returns one
fn takes_and_gives_back(a_string: String) -> String { // a_string comes into
                                                      // scope

    a_string  // a_string is returned and moves out to the calling function
}
```