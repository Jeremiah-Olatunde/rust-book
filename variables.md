---
source: https://doc.rust-lang.org/book/ch03-01-variables-and-mutability.html
tags:
  - draft
  - rust
---

***variables***  
variables are immutable by default

```rust
fn main() {
    let x = 25;
    println!("{x}"); // 25
    x = x + 1; // reassignment throws a compiler error
}
```

the `mut` allow variables to be mutable 

```rust
fn main() {
    let mut x = 25;
    x = x + 1; // valid reassignment
    println!("{x}"); // 26
}
```

***constants***  
constants are immutable values defined with the `const` keyword that can not be changed.  
- the `mut` keyword can not be used with constants  
- must be type annotated  
- can be declared in any scope including the global scope.  
- may not be set to values that can only be computed at run time ([see](https://doc.rust-lang.org/reference/const_eval.html))

```rust
const SPEED_OF_LIGHT = 299792458;
```

***shadowing***  
declaring a new variable with the same name as an existing variable. Note the difference between reassignment and shadowing. shadowing is essentially creating an entirely new variable. Hence the type can be changed, `mut` is not needed etc.

```rust
fn main() {
    let x = 5;
    let x = x + 1;
    println!("{x}"); // 6 
    {
        let x = x * x;
        println!("{x}"); // 36
    }
}
```