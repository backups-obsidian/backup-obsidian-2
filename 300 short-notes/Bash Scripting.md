---
created: 2022-11-28 16:02
updated: 2022-11-28 19:51
---
---
**Links**: 
- [[300 home]]
- [[Strict mode in bash]]

---
## Bash Scripting
### Variables
- Defining variables: `variable_name=34`
	- Storing the output of a command: `variable=$(pwd)`
	- **There should be no space on either side of `=`**.
- We refer variables using `$variable_name`

> [!note]+ *Always name you variables in lower case*. 
> In general upper case variable names mean they are defined by the system. Like `$USER` or `$HOME`

- **Double quotes** are used for **variable expansion**, **single quotes** treats the strings **as is without any expansion**.

### Math Operations
- Arithmetic operation: `expr 80 + 10`, `expr 80 - 10`, `expr 80 / 10`
- **`*` won't work for multiplication since `*` is a wild card in bash**.
	- `expr 80 \* 10`
- Using variables: `expr $var1 + $var2` 

### Exit codes
- Status of the last command: `echo $?`

## TODOs
- Arrays in bash
- Inputs in bash
- EOF in Bash

## References