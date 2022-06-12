---
created: 2022-05-17 21:39
updated: 2022-05-17 21:47
---
---
**Links**: [[105 Rust Index]]

---
- The **prelude** is the list of things that Rust automatically imports into every Rust program. It's kept as small as possible, and is focused on things, particularly traits, which are used in almost every single Rust program.
	- Like we are able to print without importing anything.
	- But user import is not included in prelude.

- **Crate** in rust is *synonymous to packages/libraries* in other languages.
- In rust we *import modules from crates*.
- From these modules we can use specific functions in our code.
- `use std::io;`. In this `std` is the crate and `io` is the module.

```rust
use std::io;

fn main() {
 let mut input = String::new();
 io::stdin().read_line(&mut input).expect("failed to read");
}
```

- We are grabbing the `stdin` function from the `io` module.
- We have to pass the input variable like this because if we only pass input it will just pass the value. So by using the `&` we passing the reference of input variable. But references are immutable so we include the keyword `mut`.