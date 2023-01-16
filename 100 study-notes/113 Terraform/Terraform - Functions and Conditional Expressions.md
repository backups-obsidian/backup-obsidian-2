---
created: 2023-01-14 19:23
updated: 2023-01-14 19:37
---
---
**Links**: [[113 Terraform Index]]

---
## Functions
- Some of the common functions are 
	- `file`: read data from a file 
	- `length`: determine the number of elements in a list or map
	- `toset`: convert a list to a set
 - We can test functions using an interactive console.
	- We can use the terraform console using `terraform console`
	- Terraform console *loads the state associated to the configuration directory by default*.
	- It also loads variables in the configuration files.
	- ![[attachments/Pasted image 20230114192353.png]]
- Numeric functions
	- **Expansion is needed when using variables**.
	- ![[attachments/Pasted image 20230114192605.png]]
- String functions
	- ![[attachments/Pasted image 20230114192840.png]]
	- `split` is used to split a string based on the separator. It *returns a list*.
	- `join` is used to convert a list to a string.
	- ![[attachments/Pasted image 20230114192916.png]]
- List functions
	- ![[attachments/Pasted image 20230114193039.png]]
	- `contains` is case sensitive
 - Map functions
	- ![[attachments/Pasted image 20230114193129.png]]
	- We can provide a default argument to the `lookup` function
	- ![[attachments/Pasted image 20230114193207.png]]

## Conditional Expressions
- Assigning value to a variable based on condition
	- Generating a random password of atleast 8 characters where length of password comes from the user.
	- ![[attachments/Pasted image 20230114193611.png]]