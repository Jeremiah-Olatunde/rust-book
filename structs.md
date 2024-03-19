---
source: https://doc.rust-lang.org/book/ch05-01-defining-structs.html
tags:
  - rust
  - draft
---
structs hold multiple related named values. 
the named nature of the values means that the specification and accessing of data can be done out of order.
to defined a struct the field names and types are specified like so `field: type,`.

> [!note]
> Structs name use `CamelCase` convention which is enforced by the compiler

```rust
struct Person {
  name: String,
  age: u8,
  height: f64,
}
```

```rust
// creating and instance of struct
// note field specification order differs from that in the struct definition
let jesuseun = Person {
  age: 22,
  height: 190.0,
  name: String::from("Jesuseun Jeremiah Olatunde"),
};

// accessing struct fields with the dot notation
println!(
  "hello my name is {}. I am {} years old", 
  jesuseun.name, 
  jesuseun.age
);
```

```rust
// the struct must be marked as mutuable for mutation
let mut jesuseun = Person {
  age: 22,
  height: 190.0,
  name: String::from("Jesuseun Jeremiah Olatunde"),
};

jesuseun.age += 1;
jesuseun.name = String::from(jesuseun.name.split(" ").next().unwrap());

println!("{:?}", jesuseun.name);
```

> [!note]
> the similarity between structs and tuples. tuples store related data of different types similarly to structs but unlike structs rely on speciation order for data access and definition

> [!note]
> unlike tuples structs do not automatically implement the `Copy` trait even if a struct is completely composed of types that do implement the `Copy` trait.
> 

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

fn take_ownership<T>(_rect: T){

}

fn main(){

    let rect_tuple = (5u32, 5u32);

    let rect_struct = Rectangle {
        width: 5,
        height: 5,
    };
    
    let rect_tuple_str: (String, String) = (
      String::from("5"), 
      String::from("5")
     );

    take_ownership(rect_tuple);

    // rect_tuple implements Copy trait
    // rect_tuple copied not moved hence is still available
    println!("{:?}", rect_tuple); 

    take_ownership(rect_struct);

    // rect_struct moved instead of copied
    // does not implement the copy trait
    println!("{:?}", rect_struct);

    take_ownership(rect_tuple_str);

    // rect_tuple_str contains String which does not implement Copy
    // rect_tuple_str does not implement Copy
    // hence it was moved and not available here
    println!("{:?}", rect_tuple_str);
}
```

> [!question]
> - how are structs and tuples represented and stored in memory.

```rust
struct TestStruct {
    foo: u8,
    bar: String
}

fn main(){
    let test_struct: TestStruct = TestStruct {
        foo: 255,
        bar: String::from("hello world"),
    };

    let test_tuple: (u8, String) = (255, String::from("hello world"));
}
```

`String` itself requires `3 * usize` bytes (pointer, length and captivity) and `u8` requires 1 byte. are `test_tuple` and `test_struct` both therefore stored in a `32` byte contiguous section of memory on the stack.

***field shorthand syntax***
if a field has the same identifier as a variable currently in scope and only the field name is specified during instantiation the value of the in scope variable is used as the field value

```rust 
struct Person {
    name: String,
    age: u8,
    height: f64,
}

fn create_person(name: String, age: u8, height: f64) -> Person {
    Person { name, age, height }
}

fn main(){
    let jesuseun: Person = create_person(
        String::from("Jesuseun Jeremiah Olatunde"), 
        22, 
        190.0
    );
}
```

***struct update syntax***

```rust
#[derive(Debug)]
struct Point {
    x: u8,
    y: u8,
}

fn main(){

    let origin = Point { x: 0, y: 0 };
    let move_x_5 = Point { x: 5, ..origin };

    println!("{:?}", origin);
    println!("{:?}", move_x_5);
}
```

when using the struct update syntax fields are either moved or copied based on the rules discussed earlier. fields whose types implement the `Copy` trait are duplicated whereas fields that implement the `Drop` trait are moved therefore transferring ownership.

```rust
struct Person {
    name: String,
    age: u8,
}

fn main(){
    let name0 = Person { name: String::from("Jesuseun Jeremiah Olatunde" ), age: 22 };
    let name1 = Person { ..name0 };

    // age implements Copy trait and hence it duplicated leaving name0.age still valid
    println!("{:?}", name0.age);

    // compiler error, name field data has been moved to name1 invalidating name0.name
    println!("{:?}", name0.name);
}
```

***tuple structs***

```rust
struct Color(u8, u8, u8);
struct Point(u8, u8, u8);

fn main() {
    let black = Color(0, 0, 0);
    let origin = Point(0, 0, 0);
}
```

notice that `Point` and `Color` have an identical structure. They are both composed of the same kind and number of types and therefore represented in memory identically. However they are considered different types by the compiler. An instance of `Color` can not be passed where instances of `Point` are expected. this is called nominal typing and is contrasted by the structural typing that is seen in TypeScript.

[ read-later :: https://en.wikipedia.org/wiki/Nominal_type_system ]

***unit structs***

```rust
struct AlwaysEqual;

fn main(){
  let foo = AlwaysEqual;
}
```

***Ownership of struct data***

not that structs can not store references without the specification of lifetime parameters. this ensures that struct fields always store references to valid values. lifetime allow the compiler to verify that the referenced values will indeed be valid for at least long as the struct is

```rust 
struct Person {
  name: &str // compiler error
}
```