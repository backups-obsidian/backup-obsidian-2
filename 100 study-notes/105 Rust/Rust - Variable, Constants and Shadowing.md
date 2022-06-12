---
created: 2022-05-15 20:52
updated: 2022-05-15 21:37
---
---
**Links**: [[105 Rust Index]]

---
## Variables
- Before understanding variables we need to understand that Rust is a **statically and strongly typed** language.
- We can either explicitly define the types or leave it to the compiler to infer it which known as implicit type assignment.
	- `let x = 4` : implicit type assignment. You **cannot change the type of the variable throughout the program**.
- By **default** in rust **all variables are immutable**. To fix this we use the keyword `mut`
	- `let mut x = 4;`
- You can redefine variables in rust. This is another way of assigning a different value to a variable if you don't want to use `mut`.
	- This is not allowed in most other programming languages.
```rust
fn main(){
 let x = 4;
 let x = 5;
}
```

- While redefining variables we can change their type. Like from int to string.

## Name Shadowing
```rust
fn main() {
    let x = 4;
    println!("Value of x: {}",x);

    {
        let x = x - 2;
        println!("Value of x : {}",x);
    }

    let x = x + 1;
    println!("Value of x: {}", x);
}
```
- Output: 4,2,5

## Constants
- Naming convention is to use *all upper case* letters *separated by underscores*.
- When you define a constant **you need to define its type**. This is compulsory. Defining the type was optional with normal variables.
	- `const TIME_SECONDS: u32 = 60;`
- Unlike a variable **constants cannot be redefined**.
```rust
fn main(){
 const TIME_SECONDS: u32 = 60;
 // this wrong and will give an error
 const TIME_SECONDS: u32 = 67;
}
```
