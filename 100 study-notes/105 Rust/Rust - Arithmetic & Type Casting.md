---
created: 2022-05-15 20:52
updated: 2022-05-20 21:57
---
---
**Links**: [[105 Rust Index]]

---
- Different datatypes cannot be added together
```rust
let x: i8 = 12;
let y: u8 = 10;
let z = x + y; // this will give an error
```
- We cannot add same class different precision values for example `i32` + `i64` would give an error.
- The result is same type as that of the operands. If the operands are `i8` then the result will also be `i8` unless explicitly type casted.
- Multiple ways of defining the type of operand 
```rust
let a: i8 = 12;
let x = 12_i8;
let y = 12 as i8;
let z = 12i8;
```
- `as` is how we do **explicit type conversion**
```rust
let x: i8 = 45;
let y: i32 = 566;
let z = (x as i32) + y;
```
- In the above example we are going from a smaller to a larger type, *if you go to a smaller type from a larger type then an overflow will occur*.