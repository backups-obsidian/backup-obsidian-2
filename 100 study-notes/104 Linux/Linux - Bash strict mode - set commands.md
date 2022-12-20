---
created: 2022-12-19 19:25
updated: 2022-12-19 19:25
---
---
**Links**:
- [[104 Linux Index]]
- [[Linux - Bash strict mode]]

---
## Rules
### `set -e`
- The `set -e` option instructs bash to **immediately exit** if any command has a **non-zero exit status**.
	-  The obvious exception here are *commands executed inside loop conditions*, *conditionals* or as *arguments to logic operators* (`||`, `&&`).
- Example:

```bash
echo "hello worlds"
cd nonexistent
echo "after"

# Output:
# hello worlds
# ./test.sh: line 4: cd: nonexistent: No such file or directory
# after
```

```bash
set -e
echo "hello worlds"
cd nonexistent
echo "after"

# Output:
# hello worlds
# ./test.sh: line 4: cd: nonexistent: No such file or directory
```

- Notice that bash scripts stops at the point when we get a return code of 1 (`cd nonexistent`).

### `set -u`
- `set -u` *affects variables*. 
- When set, a *reference to any variable you haven't previously defined* - with the exceptions of `$*` and `$@` - **is an error**, and *causes the program to immediately exit*.
	- Languages like Python, C, Java and more all behave the same way, for all sorts of good reasons. One is so typos don't create new variables without you realising it.

```bash
set -u

hello=10
echo "test script"
echo $Hello

# Output:
test script
./test.sh: line 5: Hello: unbound variable
```

- Without the `-u` option, this will be a silent error. 
- But with the `-u` option, the *script exits on that line with an exit code of 1*.

### `set -o pipefail`
- This setting *prevents errors in a pipeline from being masked*. 
- If any **command in a pipeline fails**, *that return code will be used as the return code of the whole pipeline*. 
- By default, *the pipeline's return code is that of the last command - even if it succeeds*.

```bash
ls -alh | cd nonexistent | echo "hello"
echo $?

# Output:
# ./test.sh: line 3: cd: nonexistent: No such file or directory
# hello
# 0
```

- Notice that even though `cd nonexistent` failed since the last command in the pipeline was successful the exit code of the whole pipe was 0.

```bash
set -o pipefail
ls -alh | cd nonexistent | echo "hello"
echo $?

# Output:
# ./test.sh: line 3: cd: nonexistent: No such file or directory
# hello
# 1
```

- Notice that now the exit code is 1 since `cd nonexistent` failed. 
	- Still it won't quit the program. 
	- We need `set -e` along with it to quit the program if we encounter any exit code other than 0.