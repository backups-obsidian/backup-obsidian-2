---
created: 2022-06-15 14:36
updated: 2022-06-15 19:00
---
---
**Links**: [[104 Linux Index]]

---
> [!important] Always on the *right hand side of the pipe*.

### Why use `xargs`
- Many Linux commands *support both standard input (Stdin) and command arguments*, such as `sed`, `grep`, and `awk`.
- On the other hand, **some commands don’t read Stdin and only support arguments**  `cp`, `rm`, `ls`, and `mv`. 
- So, **when we want to execute these commands with Stdin as the input, we need to convert Stdin into arguments**. The `xargs` command is born to do that.
- It is usually used in combination with other commands through **piping**.
- `echo "file1 file2 file3" | xargs touch`
	- In the example above, we are *piping the standard input* to `xargs`, and the command is run for each argument, creating three files. This is the same as if you would run: `touch file1 file2 file3`

### Individual lines
- The `-n` (`--max-args`) option specifies the number of arguments to be passed to the given command. 
- `xargs` runs the specified command as many times as necessary until all arguments are exhausted.
- `echo  "file1 file2 file3" |  xargs -n 1 -t touch`: The touch command is executed separately for each argument
```bash
touch file1
touch file2
touch file3
```
- Useful when some commands won't take multiple arguments

### Useful switches
- To **print the command on the terminal before executing it** use the `-t` option
- Make `xargs` ask for permission before executing the command using `-p`
- `fd test | xargs -t -p -n 1 rm -rf` : In this example `xargs` **would ask for permission for all files**.  Important example to understand `-n 1` flag.
	- ![[attachments/Pasted image 20220615150614.png]]
- `fd test | xargs -t -p rm -rf` : Will only ask for permission once
	- ![[attachments/Pasted image 20220615150658.png]]
- `-P` number of processes to use

### With `find`
- `xargs` is most often used in combination with the command. 
	- You can use `find` to search for specific files and then use `xargs` to perform operations on those files.
- `find /var/www/.cache -type f  | xargs rm -f`
	- In the above example, `find` will print the full names of all files inside the `/var/www/.cache` directory and `xargs` will pass the file paths to the `rm` command.

#### Files with spaces
- `xargs` **does not automatically include files which contain blank spaces in their names**. 
- To include those files too, use the `-print0` option for `find`, and the `-0` option for `xargs`
	- `find [location] -name "[search-term]" -type f -print0 | xargs -0 [command]`
- **Example**
	- ![[attachments/Pasted image 20220615161211.png]]
	- ![[attachments/Pasted image 20220615161245.png]]
	- The above happened because of space between the file name "hello world"
	- ![[attachments/Pasted image 20220615161351.png]]
	- This works since `-print0` uses `\0` (null) as separator. This can be seen using hexdump

#### `xargs` vs `exec`
The `find` command supports the `-exec` option that allows arbitrary commands to be performed on found files. The following are equivalent.

```
find ./foo -type f -name "*.txt" -exec rm {} \; 
find ./foo -type f -name "*.txt" | xargs rm
```

> [!note]- But `xargs` is *far more efficient*.
> You can verify this by using `time` in front of the command.

### With `grep`
- Using `xargs` with `grep`
	- `find • -name '*. txt' | xargs grep 'example'`
	- The example above searched for all the files with the `.txt` extension and piped them to `xargs`, which then executed the `grep` command on them.

### Run multiple commands with `xargs`
- It is possible to run multiple commands with `xargs` by using the `-I` flag. This replaces occurrences of the argument with the argument passed to `xargs`.
- The following echos a string and creates a folder.
```bash
> cat foo.txt | xargs -I % sh -c 'echo %; mkdir %' 
one 
two 
three 
> ls 
one 
two 
three
```

### Insert arguments at a particular position
- The `xargs` command offers options to **insert the listed arguments** at some *arbitrary position* other than the end of the command line.
- The `-I` option takes a **string that gets replaced with the supplied input** before the command executes. 
	- Although this string can be any set of characters, a common choice for it is `%`.
- `find ./log -type f -name "*.log" | xargs -I % mv % backup/`
- Another way : `find ./log -type f -name "*.log" | xargs bash "mv $1 backup/"`
- If you use it again and again then the same argument will be used
	- `ls * | xargs -n 1 -I % cp % %.bak`
	- Create a `.bak` file for all the files in the directory

### Types of arguments
- Commands can have multiple arguments in two scenarios:
	- **All command arguments** – `COMMAND ARG1 ARG2 ARG3`
		- All the above examples were all command arguments
	- **Option arguments** – `COMMAND -a ARG1 -b ARG2 -c ARG3`

#### Option arguments example
- `echo "Tom Likes Jerry" | xargs bash -c './threeOptions.sh -A $0 -B $1 -C $2'`
	- or `echo "Tom Likes Jerry" | xargs bash './threeOptions.sh -A $1 -B $2 -C $3'`
	- or `echo "Tom Likes Jerry" | xargs sh './threeOptions.sh -A $1 -B $2 -C $3'` 
- We need to keep in mind that if we use `bash -c the_real_command`, the first argument is assigned to `$0` instead of `$1`
- Since the *arguments have been indexed*, we can *easily change the arguments’ order* or decide which argument to pass to which option.
	- `echo "Tom Likes Jerry" | xargs bash -c './threeOptions.sh -A $2 -B $1 -C $0'`

### Example: Delete log files older than 7 days
- `find . -type f -mtime +7 | rm` - This prints an error message since `rm` *expects arguments and can’t read them from STDIN*
- `find . -type f -mtime +7 | xargs rm` - solution

### Miscellaneous
- By default if you don't give `xargs` a second command it will use `echo`
	- `ls * | xargs` is same as `ls * | xargs echo`

### References
- [xargs With Multiple Arguments | Baeldung on Linux](https://www.baeldung.com/linux/xargs-multiple-arguments)