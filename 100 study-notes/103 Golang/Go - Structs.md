---
created: 2022-05-15 21:47
updated: 2022-05-29 12:38
---
---
**Links**: [[103 Golang Index]]

---
## Structs
- Struct is a **sequence of named elements, called fields**. 
	- Each of them has a name and a type.
- If you are familiar with OOP from other languages you can think of a *struct as of a class*.
	- The struct *fields are like the instance attributes we define in OOP*.
- Unlike traditional Object-Oriented Programming, **Go does not have a class-object architecture**. 
	- Rather we have *structs which hold complex data structures*.
- A structs is *nothing more that a **schema** containing a blueprint of data a structure will hold*. 
	- This blueprint is **fixed at compile time**. 
	- It's not allowed to change the name or the type of the fields at runtime. 
	- You can't add or remove fields from a struct at runtime.

### Creating Structs
- In the below example order matters
```go
type book struct {
	name, author string // types of the same type can be on the same line
	year int
}
book1 := book{"abs", "deb", 1923} 
// book is struct type whereas book1 is a struct
// In OOP book1 would have been an object and book would have been a class
```
- Above is not the recommended way to initialise structs
```go
book2 := book{title: "abc", author: "drd", year: 1289}
// order doesnot matter anymore
```
- If we create a struct by omitting some values of fields, they will be assigned default values.

### Retrieving and updating fields
- To retrieve fields and update we use the `.` operator
```go
fmt.Println(book1.title)
book1.title = "new title"
```

### Comparison of Structs
- We can compare structs using `==` sign
- For 2 structs to be equal all the fields must have the same value

### Creating a copy
- We can use `=` or `:=` to create a copy. 
	- This is contrary to what happens in maps and slices
```go
type book struct {
	name string
	year int
}

book1 := book{name: "abc", year: 1928}
book2 := book1
book2.year = 9999
fmt.Println(book1, book2) // {abc 1928} {abc 9999}
// only book2 was modified
```

### Anonymous Structs
```go
book3 := struct {
	title, author string
	year int
}{
	title: "abc",
	author: "cde",
	year: 2343,
}
```
- In the above example we have created a book3 struct without defining a struct type alias
- This is useful when we *don't want to reuse a struct type*
- Anonymous fields
```go
type book struct {
	string
	int
}
b1 := book{"abc", 1234}
fmt.Println(b1.string) // abc
```
- We can mix anonymous fields with named fields
```go
type book struct {
	title string
	year int
	bool
}
```

### Embedded Structs
- An embedded struct is just a struct that acts like field in another struct
- The advantage is that embedded structs can also be used as standalone structs
```go
type address struct {
	city, country string
}
type employee struct {
	name       string
	newAddress address
}
emp1 := employee{
	name: "Sarthak",
	newAddress: address{
		city:    "abc",
		country: "dde",
	}, // important since we are declaring multiline structs
}
fmt.Println(emp1.newAddress.city) // abc
fmt.Printf("%+v", emp1) // {name:Sarthak newAddress:{city:abc country:dde}}
```

### Miscellaneous
```go
type person struct {
	name   string
	age    int
	colors []string
}
me := person{
	name: "Marius", 
	age: 30, 
	colors: []string{"red", "yellow"},
}
```