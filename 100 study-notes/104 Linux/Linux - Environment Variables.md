---
created: 2023-05-15 12:25
updated: 2023-05-15 12:35
---
---
**Links**: [[104 Linux Index]]

---
## Environment Variables
- The names of variables are case sensitive.
- By convention environment variables have upper case names.
- When assigning multiple values to the variable they must be separated by the colon `:` character.
- There is no space around the equals `=` symbol.

- Variables can be classified into **two main categories**, 
	- *Environment variables*: are variables that are **available system-wide** and are **inherited by all spawned child processes and shells**.
	- *Shell variables*: are variables that **apply only to the current shell instance**.

### Some commands
- `env` → The command allows you to run another program in a custom environment without modifying the current one. 
	- When used without an argument it will print a list of the current environment variables.
- `printenv` → The command *prints all or the specified environment variables*.
- `set` → The command sets or unsets shell variables. When used without an argument it will print a list of all variables including environment and shell variables, and shell functions.
- `unset` → The command deletes shell and environment variables.
- `export` → The command sets environment variables.

### Print a specific environment variable
```bash
printenv NAME
```

```bash
echo $NAME
```

> [!important] The `echo` command **can print environment and shell variables** whereas **`printenv` can only print environment variables**.

### Setting shell variables
```bash
TEST_VARIABLE='value'
```

- We can display it using echo. If we do

```bash
printenv | grep TEST_VARIABLE
```

- *We will get no output since it is not an environment variable*.
- If you close the shell the variable will be deleted.

### Setting environment variables
```bash
export TEST_VARIABLE='value'
```

- If you close the shell the environment variable will be lost.
- One of the most common use of the export command is when you want to add something to the path so that your Linux system will find the certain command/executable file. 
	- What does it do? It adds this directory location to the path. When you try to run a command in Linux, your system searches for its executable (usually in bin directory) in the directories mentioned in the PATH variable.

### Creating persistent environment variables
- Different ways of creating persistent environment variables:
	- ![[attachments/Pasted image 20230515123127.png]]

### Use of `export`
- `export` **makes the variable available to sub-processes**.

```bash
export name=value
```
- This means that the variable name is available to _any process_ you run from that shell process. 
- If you want a process to make use of this variable, use `export`, and run the process from that shell.

```bash
name=value
```

- The above means the **variable scope is restricted to the shell, and is NOT available to any other process**. 
- You would use this for (say) loop variables, temporary variables etc.

> [!note] **Exporting a variable DOESN'T make it available to parent processes**.
> That is, specifying and exporting a variable in a spawned process doesn't make it available in the process that launched it.

- Example
	- ![[attachments/Pasted image 20230515123337.png]]
