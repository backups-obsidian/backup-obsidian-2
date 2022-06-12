---
created: 2022-05-15 20:52
updated: 2022-05-20 21:45
---
---
**Links**: [[105 Rust Index]]

---
- There are 2 types of data types in Rust: 
	- **Scalar**: 
		- A scalar data type is something that has a *finite set of possible values*, following some scale, i.e. each value can be *compared to any other value* as either equal, greater or less.
		- Integer, boolean, character, floating point.
	- **Compound**:
		- In compound data type is any data type which can be *constructed* in a program using the programming language's *primitive data types* and other compound types.
		- Array, tuple

## Scalar Types
- Signed and Unsigned (only positive integers) integer: `i32`(**default**) & `u32`. We have 8,16,32,64 & 128.
	- `let value: u32 = 67;`
- Floating point: `f32` & `f64`. 
	- `f64` is the **default** type for implicit types.
	- `let value: f32 = 6.7;`
- Boolean: `bool`. `true` also represented by 1.
	- `let value: bool = true;`
- Characters: `char`
	- `let value: char = 'a';`

## Compound Types
### Tuple
- Tuple: **Fixed length** sequence of elements which is **immutable**.
- `let tup: (i32, bool, char) = (1,true,'s');`
- The type of the tuple can also be implicitly determined by compiler.
- Referring elements inside a tuple: `println!("{} ", tup.1);`
- If a tuple is *mutable* then it can be *modified*.
```rust
fn main(){
 let mut tup: (u32, bool, char) = (5,true,'s');
 tup.0 = 10;
 tup = (54, false, 'a');
}
```
- **Cannot add elements** to the tuple.

### Arrays
- Arrays need to have **same element** inside them.
- We **cannot add more elements** to an array.
- `let arr: [i32;5] = [1,2,3,4,5];`
- Type can be implicitly determined by the compiler.
- Array can only be *modified* if *mutable*.
- `arr[0]` to access the elements of the array.