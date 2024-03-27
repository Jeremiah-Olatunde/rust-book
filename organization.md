---
source: https://doc.rust-lang.org/book/ch07-00-managing-growing-projects-with-packages-crates-and-modules.html
tags:
  - rust
---
- packages
    - a cargo feature for building, testing and sharing crates
- crates
    - a tree of modules that produce a library or executable
- modules
    - enables control and management of organization, scoping, privacy and paths
- paths
    - a method of naming an item (struct, function, enum, modules) within a scope

A crate is the smallest unit of code considered by the compiler at a time. A crate can contain one or more modules. These modules can be in the same file or placed in separate files.

> [!note]
> The whole module management system seems detached from the file system. It merely draws inspiration from the file systems but is not dependent on it like in the case of JavaScript where each file with an `export` is a module.  
> 
> One could have a crate with an entire complex module tree, using paths and privacy features all in one file.

There are two types of crates; binary and library crates. 
- Binary crates compile to an executable binary, code that can be distributed and run on a machine. As such all binary crates must have a main function. 
- Library crates are not compiled to an executable. Instead they provide functionality intended to be shared with external code. Other library crates or binary crates that then compile the included functionality into an executable

The *crate root* is the source file which the the compiler starts from and makes up the root module of a crate. for binary crates the crate root is `src/main.rs` for library crates the crate root is `src/lib.rs`. Both these crates are assumed to have names that are the same as the root folder of the project. (note the concept of the file system being detached).

A package is a bundle of one or more crates. A package can contain multiple binary crates but only one library crate.

> [!tip]
> In general binary crates have just enough code to start an executable that calls into a library crate. The majority of the functionality is put in the library crate. This allows other projects to to use the functionality in the library crate

Modules enable code organization within a create. Items within a module are private by default and must be explicitly *exported* using the `pub` keyword. Modules may be split into separate files or placed into one file

```rust
// crate root scope i.e resturant scope
mod front_of_house {
    mod hosting {
        fn add_to_waitlist() {}

        fn seat_at_table() {}
    }

    mod serving {
        fn take_order() {}

        fn serve_order() {}

        fn take_payment() {}
    }
}
```

![[Pasted image 20240327133206.png]]

paths are used to access items in a module tree. there are two types of paths; absolute paths and relative paths.
- an absolute path is the full path starting from the crate root
- a relative path starts from the module in which the path is specified and may use `super`, `self` or some identifier in the current module

all items within a module must be marked a public with the `pub` keyword in order to be accessible using paths. these include functions, structs, enums and even modules themselves.

marking only a module public makes only the module identifier accessible using a path but not the contents of the module.

> [!note]
> parent modules do not have access to the items defined within their child modules. However child modules have access to the items defined within their parent.

```rust
#[allow(dead_code)]
mod a {
    pub mod b {
        pub fn ab(){
            println!("AB");
        }
    }

    // c is public and accessible to outer scope
    // ac is not
    pub mod c {
        fn ac(){
            println!("AC");
        }
    }
}

fn main() {
    a::b::ab();
}
```


the `super` keyword is used to construct relative paths that begin from the parent modules

```rust
#[allow(dead_code)]
mod a {
    pub mod b {
        pub fn ab(){
            println!("AB");
        }

        pub mod g {
            use super::super::c::ac;

            pub fn abg(){
                println!("ABG");
                println!("calling AC from abb");
                ac();
            }
        }
    }

    pub mod c {
        use super::b::ab;

        pub fn ac(){
            println!("AC");
            println!("calling AB from ac");
            ab();
        }
    }
}

fn main() {
    a::c::ac();
}
```

there exist caveats when make structs and enum module items public.

similar to modules placing `pub` before a struct definition makes the struct public but not the fields of the struct. those remain private and must be individually made public. 

Field privacy applies both when accessing the field of a struct and when creating a struct. Ergo if a struct has private fields then instances of that struct can not be created outside of the scope in which it is defined. A public constructor function must be written in order to create the struct. 

```rust
mod a {
    pub struct Person {
        pub name: String,
        secret: String,
    }

    impl Person {
        pub fn new(name: String, secret: String) -> Self {
            Person {
                name,
                secret
            }
        }
    }
}

fn main() {
    // Person can not be created because secret is private
    let error_jesuseun = a::Person {
        name: String::from("Jesuseun Jeremiah Olatunde"),
        secret: String::from("I have no secrets"),
    };

    let jesuseun = a::Person::new(
        String::from("Jesuseun Jeremiah Olatunde"),
        String::from("I have no secrets"),
    );

    // secret field can not be accessed
    println!("{:?}", jesuseun.secret);
}
```

if an enum is made public all of its variants are public

the `use`  keyword can be used to bring paths into scope. The identifier of item the path refers to is brought into the scope in which the use keyword is specified. Note that items brought into scope with `use` are only available in that scope, child modules must also specify `use`  if they wish to bring the item into their scope.

```rust
mod a {
    use b::ab;

    pub mod b {
        pub fn ab(){}
    }

    pub mod c {
        use super::ab;
        pub fn bc(){
            ac()
        }
    }
}
```

the `as` keyword can be used to bring an item into scope under a different identifier.

```rust
use std::io::Result as IOResult;
```

`pub use` is used to re-export items. Re-exporting is useful when the internal structure of your code is different from how programmers calling your code would think about the domain.

```rust
// external modules can now just do
// use mod_name::abc
pub use a::b::c::abc
```

in order to use external packages the package name and version is specified in  `Cargo.toml` 

```rust 
rand = "0.8.5"
```

Cargo then installs the crate from [crates.io](https://crates.io). The definition in the package are brought into scope using the `use` keyword.

```rust
use rand::Rng
```

nested paths provide a convenient syntax for importing multiple items defined in the same crate or module

```rust
use std::cmp::Ordering;
use std::io;

// replace with
use std::{cmp::Ordering, io};
```

```rust
use std::io;
use std::io::Write;

// replace with
use std::io::{self, Write}
```

the glob operator bring all items define in a module into the current scope

```rust
use std::collection::*;
```

as mentioned earlier modules can be split into multiple files

![[Pasted image 20240327153910.png]]
