---
created: 2022-12-19 00:01
updated: 2022-12-19 19:23
---
**Links**:
- [[104 Linux Index]]
- [[Linux - Bash strict mode]]

---
## Issues & Solutions 
- There are some things that don't work under bash strict mode that would have worked under normal mode.

### Sourcing A Nonconforming Document
```bash
source some/bad/file.env
# Your strict-mode script immediately exits here,
# with a fatal error.
```

- The solution to this is to temporarily turn off the strict mode, source the file and then turn the strict mode on.

```bash
set +u # disable
source some/bad/file.env
set -u # enable
```

- Remember, `set +u` _disables_ this variable strictness, and `set -u` _enables_ it. 
	- A bit counterintuitive.
- Sourced documents rarely need `-e` or `-o pipefail` to be disabled. 
	- But if you ever encounter that, you deal with it the same way.

### Positional Parameters
- The *`-u` setting causes the script to immediately exit if any undefined variable references are made, except for `$*` or `$@`*. 
- But what if your script takes positional arguments - `$1`, `$2`, etc. - and you want to verify (using `-z` test) that they were supplied? Consider this script named `sayhello.sh`
	- `./sayhello.sh: line 3: $1: unbound variable`
- The solution is to use **default values**
	- *bash has a syntax for declaring a default value, using the `:-` operator*.

```bash
# Variable $foo has not been set. In strict mode,
# next line triggers an error.
bar=$foo

# ${VARNAME:-DEFAULT_VALUE} evals to DEFAULT_VALUE if VARNAME undefined.
# So here, $bar is set to "alpha":
bar=${foo:-alpha}

# Now we set foo explicitly:
foo="beta"

# ... and the default value is ignored. Here $bar is set to "beta":
bar=${foo:-alpha}

# To make the default an empty string, use ${VARNAME:-}
empty_string=${some_undefined_var:-}
```

- Under strict mode
```bash
name=${1:-}
if [[ -z "$name" ]]; then
    echo "usage: $0 NAME"
    exit 1
fi
echo "Hello, $name"
```

### Intentionally undefined variables
- In the *default mode*, a *reference to an undefined variable evaluates to an empty string* - a behaviour that is sometimes relied on. 
	- That's not an option in strict mode. 
- To solve it explicitly set the variable to an empty string early on in the script, before any reference to it.

```bash
someVar=""
# ...
# a bunch of lines of code that may or may not set someVar
# ...
if [[ -z "$someVar" ]]; then
# ...
```

### Commands You Expect To Have Nonzero Exit Status
- What happens when you want to run a command that will fail, or which you know will have a nonzero exit code? 
	- You don't want it to stop your script, because that's actually correct behaviour.
- There are two choices here. 
	- The simplest, which you will usually want to use, is to append "`|| true`" after the command
	- Other choice is to disable error code checking before running that section.

```bash
# choice 1

# "grep -c" reports the number of matching lines. If the number is 0,
# then grep's exit status is 1, but we don't care - we just want to
# know the number of matches, even if that number is zero.

# Under strict mode, the next line aborts with an error:
count=$(grep -c some-string some-file)

# But this one behaves more nicely:
count=$(grep -c some-string some-file || true)

echo "count: $count"
```

- This short-circuiting with the boolean operator makes the expression inside `$( ... )` always evaluate successfully.

```bash
# choice 2

# We had started out this script with set -e . And then...

set +e
count=$(grep -c some-string some-file)
retval=$?
set -e

# grep's return code is 0 when one or more lines match;
# 1 if no lines match; and 2 on an error. This pattern
# lets us distinguish between them.

echo "return value: $retval"
echo "count: $count"
```

### Essential Clean-up
- Suppose your script is structured like:
	- Spin up some expensive resource
	- Do something with it
	- Release that resource so it doesn't keep running and generate a giant bill
- For "expensive resource", this can be something like an EC2 instance that costs you real money. 
- Or it could be something much smaller - like a scratch directory - that you want to create for the script to use, then be sure to delete once it is complete (so it doesn't leak storage, etc). 
- *With the `set -e` option, it is possible an error will cause your script to exit before it can perform the cleanup, which is not acceptable*.
- The solution to this is using **bash exit traps**.
	- You *define a bash function that performs the clean-up* or release of resources, and then register the function to be *automatically invoked on exit*.

```bash
scratch=$(mktemp -d -t tmp.XXXXXXXXXX)
function finish {
  rm -rf "$scratch"
}
trap finish EXIT

# Now your script can write files in the directory "$scratch".
# It will automatically be deleted on exit, whether that's due
# to an error, or normal completion.
```

### Short-Circuiting Considerations
- In case of short-circuit even if one of the expressions returns an exit code of other than 0 `set -e` won't exit the script abruptly.

```bash
cd nonexistent && echo "nonexistent directory found"

echo "Program ends"

# Output:
# ./test.sh: line 6: cd: nonexistent: No such file or directory
# Program ends
```

- You can all freely use short-circuiting, but *simply don't allow a short-circuit expression to be on the last line of a script, for anything actually deployed*. 
	- This may work 100% reliably for you and your team, but I don't believe that is the case for myself and many other developers.

- Reason:
```bash
# test.sh
cd nonexistent && echo "nonexistent directory found"
```

- This can be thought of as a bug
	- ![[attachments/Pasted image 20221219002448.png]]
	- Even though the program exited successfully the exit code is not one.

- **Perhaps a safer option is to decide never to use short-circuiting at all, and always use full if-statements instead**. 

### Empty array
```bash
# This will sometimes break, depending on the version of Bash.
# The intended behavior is to have this code break.
ARR=();
echo "All elements in the array:" "${ARR[@]}"

# Yes, it will fail even though ARR is defined.  Because it is an empty
# array, Bash treats it as undefined.

# Option 1
# Wrap in a conditional
if [[ ${#ARR[@]} -gt 0 ]]; then
    echo "All elements in the array:" "${ARR[@]}"
fi
```