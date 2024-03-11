---
source: https://doc.rust-lang.org/book/ch03-05-control-flow.html
tags:
  - rust
  - draft
---
***`if` expressions*** 

the condition in an `if` expression must be of type `bool`. no type coercion is performed.
the `else` block is optional if the return value of the `if` expression is unused.

`if` evaluates to a single value. the value is the result of the executed code block.
the ex


```rust

fn main(){
    let day_number: u8 = 0;

    let day_string = 
        if day_number == 0 { "MON"}
        else if day_number == 1 { "TUE" }
        else if day_number == 2 { "WED" }
        else if day_number == 3 { "THU" }
        else if day_number == 4 { "FRI" }
        else if day_number == 5 { "SAT" }
        else if day_number == 6 { "SUN" }
        else { "null" };

    println!("{day_string}");
}
```

the type of the value returned from all blocks must be the same

```rust
fn main(){
    let number = if true { 10 } else { "10" }; // compiler error
}
```

***loops***
rust has three kinds of loops: `loop`, `while` and `for`

`loop`  expression evaluates to the value specified after the `break` keyword otherwise `()`

```rust
fn main(){
    let mut x: u8 = 0;
    let last: u8 = loop {
        x += 1;

        if x % 2 == 0 { continue }
        if 10 < x { break x }

        println!("{x}");
    };
    
    println!("last: {last}");
}
```

loops can be labeled 

```rust
fn main(){
    let mut x: u8 = 0;

    let meaning_of_life: u8 = 'label: loop {
        x += 1;
        println!("{x}");
        if 10 < x { break 'label 42 }
    };

    println!("{meaning_of_life}");
}
```

`while` takes a condition 

```rust
fn main(){
    let mut x: u8 = 0;

    while x < 10 {
        println!("{x}");
        x += 1;
    }
}
```

`break` can not be used with values in `while` loops

`for` loops are used to iterate through a collection

```rust
fn main(){
    let test_array: [u8; 5] = [0, 1, 2, 3, 4];

    for element in test_array {
        println!("{element}")
    };

}
```

the range collection is used in conjunction with the for loop to iterate through a range of numbers

```rust
fn main(){
    for index in 0..10 {
        println!("{index}");
    }
}
```

```rust
fn recursive_fibonacci(n: u64) -> u64 {
    if n == 0 || n == 1 { 
        n
    } else {
        recursive_fibonacci(n - 1) + recursive_fibonacci(n - 2)
    }
}

fn recursive_factorial(n: u64) -> u64 {
    if n == 0 {
        1
    } else {
        n * recursive_factorial(n - 1)
    }
}

fn iterative_factorial(n: u64) -> u64 {
    let mut result: u64 = 1;

    for index in 1..(n + 1) {
        result *= index;
    }

    result
}


fn main() {
    let fib = fibonacci(10);
    let fact = factorial(10);
    println!("{fib}");
    println!("{fact}");
}

```