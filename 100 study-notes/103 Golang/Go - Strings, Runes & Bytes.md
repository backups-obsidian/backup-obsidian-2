---
created: 2022-05-28 15:08
updated: 2022-05-29 10:08
---
---
**Links**: 
- [[103 Golang Index]]
- [[Go - DataTypes]]
- [[Go - Basics]]

---
## Strings
- Strings have to be *enclosed in double quotes*.
	- If we want to use double quotes inside double quotes we must use \ to escape it
- Strings in Go are *UTF-8 encoded by default*.
- We can *raw string using backticks*. Backslashes and other special characters have no special meaning.
	- Useful for defining directories
```go
fmt.Println(`hello "there"`) // hello "there"
fmt.Println(`hello \n there`) // hello \n there
```

> [!caution]- Strings are **immutable**
> `s := "ABC";s[0] = "D"` - this will give an error

- Each time you use `+` for concatenation Go returns a new string

```go
s := "ABC"
fmt.Println(s[0]) // 65
fmt.Printf("%c",s[0]) // A
fmt.Println(s[0:2]) // AB
// for multiple characters it prints the string representation
```

- We perform *string operations* using the `strings` package.
	- Recommended way to compare strings when we don't want to take into account the case => `fmt.Println(strings.EqualFold("GO","go"))`
	- Converting to lower case and then comparing is an expensive operation


## Bytes & Runes
- Strings are implemented quite differently as compared to other programming languages.
- Go has two additional integer types called `byte` and `rune` that are *aliases* for `uint8` and `int32` data types. 
- In Go, the `byte` and `rune` data types are used to **distinguish characters from integer values**.
- Golang **doesn't have a char data type**. It uses *byte and rune to represent character values*.
	- `byte` data type represents *ASCII* characters
	- `rune` data type represents more broader *UNICODE* characters that are encode in UTF-8 by default.

> [!note]+ UTF-8 is a variable length encoding format. 
> - UNICODE characters occupy between *1 and 4 bytes*.
> - *ASCII characters occupy 1 byte*.

- **Characters or rune literals** are expressed in Go by enclosing them in **single quotes**, as in 'x' or '\n'. 

> [!important] *Rune* can be thought of being *synonymous to a letter*. Although this is true string is a slice of bytes and not runes.

- *Rune literals* such as 'a', 'b', 'c', 'x' or \n' are *represented using Unicode Code Points*. A **code point is a numeric value** that represents a rune literal.
- The character encoding scheme *ASCII which is a Unicode subset*, comprises 128 code points.
- A **string is a sequence of bytes** not runes or characters. A string is a *slice of bytes* and any byte slice can be encoded in a string value.
- The **Go terminology for code points is runes**. 
	- A rune represent a single unicode character. 
	- *Rune 0x61 in hexadecimal represents the rune literal 'a'*.

```go
a := 'a' // this is a rune
fmt.Printf("%d, %T\n", a, a) // 97, int32
str := "¥"
fmt.Printf("%d, %T, %d\n", len(str), str[1], str[1]) // 2, uint8, 165
// we have a length of 2 since UTF-8 is a variable length encoding format and the character occupies 2 bytes
// We get the byte at position 1 and not rune
fmt.Println(len("hello")) // 5
```
### Decoding a string rune by rune
- By using *indexes we get the byte at that position*, not rune.
```go
// wrong decoding
str := "¥"
for i := 0; i < len(str); i++ {
	fmt.Printf("%c\n", str[i])
} // Â¥

// using a package
for i:= 0; i < len(str); {
	r, size := utf8.DecodeRuneInString(str[i:])
	fmt.Printf("%c", r)
	i = i + size
}

str := "¥þð"
for i, r := range str {
	fmt.Printf("%d -> %c\n", i, r) 
}
// 0 -> ¥
// 2 -> þ
// 4 -> ð
```

### Understanding `len` of strings
- `len` returns the **number of bytes** in a string
	- It will be equal to the number of characters in ASCII since in ASCII each letter is 1 byte.
	- But this isn't the case with UNICODE characters which can be of multiple bytes. The rune (letter) count can't be determined using `len` function
```go
str := "golang"
fmt.Println(len(str)) // 6
str = "¥þ"
fmt.Println(len(str)) // 2 rune in the string but the length is 4
// this UNICODE string occupies 4 bytes
```
- If we *want the rune count* and not the number of bytes then we have to use a function from the `utf8` package
```go
n := utf8.RuneCountInString(str)
fmt.Println(n) // 1
```

### Slicing Strings
- Slicing a string returns bytes and not runes
```go
s := "golang"
fmt.Println(s[1:3]) // ol
```
- In the above example since it was an ASCII string where 1 character = 1 byte we got characters from index 1 to 2.
- Slicing non ASCII characters is not that simple. We need a slice of runes and not a slice of bytes.
```go
str := "¥þð"
fmt.Println(str[1:3]) // �� -> unicode representation of the returned bytes
```
- We first **convert slice of runes to slice of bytes** and then after slicing *convert the rune back to string*.
	- The above method of converting a slice of bytes to a slice of runes can also be used to find the length of the runes.
```go
str := "¥þð"
rs := []rune(str)
fmt.Println(len(rs)) // 3
fmt.Println(rs) // [165 254 240]
fmt.Println(string(rs[1:3])) // þð
```

## Miscellaneous Examples
```go
a := "hello"
b := []byte(a)
for _, value := range b {
	fmt.Printf("%#v, %d, %c\n", value, value, value)
}
// 0x68, 104, h
// 0x65, 101, e
// 0x6c, 108, l
// 0x6c, 108, l
// 0x6f, 111, o 
```
- Converting string to byte and rune slice
```go
s := "hello"
rs := []rune(s)
bs := []byte(s)
fmt.Println(rs) // [104 101 108 108 111]
fmt.Println(bs) // [104 101 108 108 111]
```
- Converting to string
```go
bs := []byte{102, 97, 65, 34}
rs := []rune{102, 97, 65, 34}
is := []uint8{102, 97, 65, 34} // alias to byte
bis := []int32{102, 97, 65, 34} // alias to rune
fmt.Println(string(bs)) // faA"
fmt.Println(string(rs)) // faA"
fmt.Println(string(is)) // faA"
fmt.Println(string(bis)) // faA"
// is := []int{102,97,65,34} // this would give an error since we cannot convert int to string
// we cannot convert any other data type to string
```