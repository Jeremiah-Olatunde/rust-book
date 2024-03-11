---
source: https://doc.rust-lang.org/book/ch03-02-data-types.html
tags:
  - rust
  - draft
---
rust is statically typed meaning the types of all values must be known at compile time.  
the compiler can typically infer the types of most values. in the event of any ambiguity the type must be explicitly annotated.

there are two kinds of data types:
- scalar
- compound

***scalar***
there are 4 primary scalar types: integers, floating-point numbers, Booleans and characters.

***integer types***  

![[Pasted image 20240311113759.png]]


signed numbers are stored using two's complement representation.  
signed variants can store numbers from -2<sup>n</sup> to 2<sup>n - 1</sup> - 1  
unsigned variants can store numbers from 0 to 2<sup>n </sup> - 1  

`isize` and `usize` depend on the system architecture and are used when indexing a collection  
the type of a number literal can be attached as a suffix e.g. `42u8`, `-69isize`.  
`_` can be used as a separator to increase readability `1_000_000usize`

an integer overflow occurs when a value outside of the range of certain integer type is stored in that type i.e. `255u8 + 1`. When compiling in debug mode overflow checks are automatically included and the program *panics* when an overflow occurs. when compiling in `--release` mode two's compliment wrapping occurs i.e. `255u8 + 1` becomes `0u8`

```rust
fn main() {
    let mut x: u8 = 255;
    x += 1;
    println!("{x}"); // 0 with --release otherwise panic
}
```

relying on this implicit behavior is not recommend. The standard library provides methods if integer wrapping is intended `wrapping_*`. checked integer methods are also provided `checked_*`

***floating point numbers***
there are two floating point primitives `f32` and `f64`. `f64` represents double precision floats and is the default, `f32` represents single precision floats.

***numeric operations***
rust has operators for all basic numeric operations, `+`, `-`, `/`, `*`, `%`. the operands of these operators must be of the same type. note that integer division truncates towards `0` to the nearest integer

**Boolean type**
`true` and `false`

***character type***
`char` is the most primitive alphabetic type. `char`s are specified using single quotes. `char`s are 4 bytes (`32bits`) 

```rust
fn main() {
    let a: char = 'A';
    println!("{a}");
}
```


***compound type***
compound types group multiple values into one type. There are two primitive compound types: tuples and arrays.

***tuple***
tuples are comma separated lists surrounded by parentheses. tuples are fixed length and may store values of identical or differing types. tuple values can be accessed using pattern matching or the dot notation syntax

```rust
fn main() {
    let color: (u8, u8, u8) = (255, 0, 0);
    let person = ("Jeremiah Olatunde", 22);

    let (x, y, z) = color;
    let name = person.0;
    let age = person.1;
}
```

***array***
arrays are comma separated lists surrounded by square brackets. arrays are fixed length and may only store values of identical types. Arrays are always a single contiguous block or memory allocated on the stack. Arrays are used when data is intended to be stored on the stack and/or when the number of elements are known.

note how the type of the array is represented. `[type; length]`
arrays can also be specified with the shorthand syntax `[value; repeat]`

```rust
fn main() {
    let x: [u8; 5] = [255, 254, 253, 251, 250];
    let y = [u8; 10] = [0; 10]; // [0, 0, ..., 0]
}
```

the elements of an array can be accessed using the square bracket notation to index into the array.

rust performs bounds checks during array indexing at runtime and panics if an out of bound index is performed.