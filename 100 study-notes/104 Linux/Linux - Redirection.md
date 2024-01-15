---
created: 2023-11-30 14:34
updated: 2023-11-30 16:21
---
---
**Links**: [[104 Linux Index]]

---
## Redirection
### Output
- We can redirect the STDOUT and STDERR of a process using `>`.
	- We redirect the STDOUT using `1>` or just `>`
	- We redirect the STDERR using `2>`
- If we want to redirect both STDOUT & STDERR of a file we can do: `sleep 1000 1>output.txt 2>output.txt &`
	- ![[attachments/Pasted image 20231130144025.png]]

- What is the use of `2>&1`?
	- It is used to *redirect STDERR to STDOUT*.
	- **The `&` in `2>&1` simply says that the number `1` is a file descriptor and not a file name**. 
		- So if we use `2>1`, then this would redirect errors to a file called `1` but if we use `2>&1`, then it would send it to the `standard output stream`.

- What is the use of `&>` or `>&`?
	- This `&>` or `>&` says **send both, `standard output` and `standard error`, somewhere**.
		- For instance, `ls non-existent-file &> out.file`

### Example
```bash
#!bin/bash

ls j1 # Does not exist
echo "koko2"
```

- Running `./koko &> output` and then checking the output file using `cat output` will result in

```bash
ls: cannot access 'j1': No such file or directory
koko2
```

- Running `./koko > output` and then checking the output file using `cat output` will result in only `koko2` but not the error output from the `ls j1` command since that will be sent to the `standard error` which we will see in the terminal.

### Order of redirection matters
- In `command 1>file 2>&1` the order of the redirection is important.
	- Let stdout be a, stderr be b and file be c. The above command states that a = c and then b = a. So b = c.
	- ![[attachments/Pasted image 20231130145324.png]]
- If you write `command 2>&1 1>file` instead, it will first redirect the command's stderr to stdout (which is generally the same) and then redirect the stdout to the file.
	- Let stdout be a, stderr be b and file be c. The above command states that b = a and then a = c. So b != c.
	- ![[attachments/Pasted image 20231130145424.png]]
	- This is same as writing `command 1>file`
- Pictorial representation of order of redirection:
	- https://catonmat.net/bash-one-liners-explained-part-three - refer point 3

> [!caution] `&>` and `>&` redirect both **stdout** and **stderr** to a file. It's normally written as `&>file` (or `>&file`). It's functionally the same as `>file 2>&1`.

- **`&>` is preferred over `>&`**
	- https://stackoverflow.com/questions/11255447/what-does-mean

### Input
- We can do input redirection using `<`
- Here document example (`<<`):
	- This is mainly used if you want to define a portion of a file in a command shell

```bash
cat << EOF > output.txt
line 1
line 2
line 3
EOF
```

### Combining Redirections
- The following example combines input and output redirection. 
	- `spell < text.txt > error.log`
	- The file text.txt is first checked for spelling mistakes, and the output is redirected to an error log file.
