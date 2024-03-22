---
source: https://doc.rust-lang.org/book/ch06-00-enums.html
tags:
  - rust
  - draft
---

***unit type***
A unit type is a type that allows only one value and therefore can hold no information.

***tagged union***
[ read-later :: https://en.wikipedia.org/wiki/Tagged_union]

A tagged union is a data type that holds a value that can be only one of any number of specified types at a time. A tag field is used to indicated which of the types the value currently is.

> [!note]
> Tagged unions save storage by overlapping storage areas for each type, since only one is in use at a time

> [!note]
> Viewed as a type with has multiple cases that must each be handled correctly when manipulating the utility of tagged unions becomes evident. 

***enumerated type***
[ read-later :: https://en.wikipedia.org/wiki/Enumerated_type]

An enumerated type (a.k.a enum) is a data type consisting of a set of value called enumerators. The enumerator names are usually constant identifiers in the language. A variable of an enumerated type can be assigned any of the enumerators as values. 

Enumerated type are not specified by the programmer as having any particular concrete representation in the computer's memory; compilers and interpreters can represent them arbitrarily.

> [!quote]
> enumerated types are often regarded as tagged unions of unit types.

> [!note]
> Enums are usually implemented with some integer type as the underlying representation. Some languages for the specification of the bit combination to be used to represent each enumerator

***rust enums***
enums are declared using the `enum` keyword  
In Rust enumerators are referred to as variants.   
the enumerators (variants) of enums are specified inside the curly brackets.  
The `IpAddrKind` enum has two unit types as its enumerators.  
Used in this manner the analogy to a tagged union of unit types is evident.

```rust
enum IpAddrKind {
  V4,
  V6
}
```

> [!question] Memory Representation
> Enums composed of unit type have no memory representation and are handled entirely at the compiler level

the names of unit enumerators are used essentially as constants identifiers. In rust these identifiers are name spaced by the enum to which they belong hence the use of `::`

```rust
let four = IpAddrKind::V4;
let six = IpAddrKind::V6;
```

Rust enumerated types depart for the *tagged union of unit types* analogy in that its enumerators may hold values within them. the names of these variants are function constructor identifiers. They take as their argument the value to be stored in the enum variant.

think of variants being able to hold all the types of structs

```rust
enum Message {
    Quit, // unit struct
    Write(String), // tuple struct
    Color(u8, u8, u8), // tuple struct
    Move { x: u8, y: u8 }, // struct
}
```

> [!question] Speculation
> when used in this manner above rust enums seem to be essentially tagged unions.   
> the implementation possibly includes a field to indicate which variant a value of an enum currently is.  
> The size of the enum in memory would be the size of the largest variant. in the case of `Message` that would probably be the `String` with size `3 * usize`

`impl` blocks can be defined for enums just like structs

***option enum***
the `Option` enum is used to represent types that might be either a value or nothing

```rust
enum Option<T> {
  Some(T),
  None // unit variant 
}

fn 

fn main(){
  let mut value
}
```

Rust avoids needing `null` values by using the `Option` type to represent types that might not exist as any point in time. As alluded to with tagged unions the value of this approach is that the compiler can enforce that all *"cases"* (`Some` and `None`) of the `Option` type are handled by the programmer.