---
source: https://doc.rust-lang.org/book/ch06-02-match.html
tags:
  - rust
  - draft
---
[ read-later :: https://en.wikipedia.org/wiki/Pattern_matching ]

the `match` is a control flow construct that allow values to be compare against a series of patterns and then executing code based on which pattern matches the value.

> [!question]
> what exactly is the formal definition of a pattern

```rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quater,
}

fn main(){
    let coin = Coin::Penny;

    let value = match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quater => 25,
    };

    println!("{}", value); // 1
}
```

the value and the patterns matched against it can be of any type but both of them must be the same type

```rust
fn pathetic_double(x: u8) -> Option<u8> {
    match x {
        0 => Some(0),
        1 => Some(2),
        2 => Some(4),
        3 => Some(6),
        4 => Some(8),
        5 => Some(10),
        _ => None,
    }
}
```

values within the value being matched on can be extracted using pattern matching. a variable is placed within the specified pattern. the portion of the value that matches with the pattern is disregarded. the remaining portion is assigned to the variable

```rust
// value   => Option::Some(5);
// pattern => Option::Some(i);
// 5 is extracted and assigned tot he variable i
```

```rust
fn add_one(x: Option<u8>) -> Option<u8> {
    match x {
        // the inner value is extracted into i
        Some(i) => if i == 255 { None } else { Some(i + 1 )},
        None => None,
    }
}

fn main(){
    println!("{:?}", add_one(Some(0)));
    println!("{:?}", add_one(Some(255)));
    println!("{:?}", add_one(None));
}
```

> [!question]
> how does pattern matching work

if only a variable there is no matching portion and the whole value is assigned to the variable


```rust
// value   => Option::Some(5);
// pattern => x
// x becomes Option::some(5)
```

this is used as a catch all case at the end of match statements


```rust
let dice_roll = 9;

match dice_roll {
    3 => add_fancy_hat(),
    7 => remove_fancy_hat(),
    other => move_player(other),
}

fn add_fancy_hat() {}
fn remove_fancy_hat() {}
fn move_player(num_spaces: u8) {}
```

if the value not used an `_` can be placed to signal this fact to the compiler

> [!note]
> the value is matched against the patterns in the order the patterns are specified. hence all catch all pattern should be placed last as subsequent pattern will never be reached

