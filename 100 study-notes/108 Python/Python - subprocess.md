---
created: 2022-12-20 19:13
updated: 2022-12-23 11:14
---
---
**Links**: [[108 Python Index]]

---
## Subprocess
- The subprocess module allows you to **spawn new processes**, *connect to their input/output/error pipes*, and obtain their return codes.
	- We can see subprocess spawning a new process using `pstree`

> [!caution]+ There is a huge security risk with `shell=True` if the input is coming from an untrusted source.
> But it enables us to write more shell like commands.
> Another advantage is that we can use shell builtin commands like `cd`. But it is better to use python `os` for changing the directory if you are going to do it for a lot of commands.

### Using `Popen`
> [!note]- `Popen` is an advanced way to run commands using python it is useful when you want to do other things when the command is running.
> If we want to wait till the program is complete then we can use a simple option `subprocess.run()`

```python
import subprocess

# not using the output and stderr
cmd1 = subprocess.Popen("ls", 
						stdout=subprocess.DEVNULL, 
						stderr=subprocess.DEVNULL)
cmd1.wait()  # wait for the process to complete
print(cmd1.stderr) # None
print(cmd1.stdout) # None
print(cmd1.returncode) # 0
```

#### Using stderr and stdout
```python
import subprocess

# if we want to use the output and stderr
# universal_newlines returns in string format.
cmd2 = subprocess.Popen(
    "ls",
    stdout=subprocess.PIPE,
    stderr=subprocess.PIPE,
    universal_newlines=True,
)
# it will return 2 values, stdout and stderr
std_out, std_err = cmd2.communicate()
print(std_out)
print(std_err)
```

#### Piping

```python
import subprocess

cmd1 = subprocess.Popen(
    "ls",
    shell=True,
    stdout=subprocess.PIPE,
    stderr=subprocess.PIPE,
    universal_newlines=True,
)

cmd2 = subprocess.Popen(
    "wc -l",
    shell=True,
    stdin=cmd1.stdout,
    stderr=subprocess.PIPE,
    stdout=subprocess.PIPE,
	universal_newlines=True,
)
stdout_cmd2, stderr_cmd2 = cmd2.communicate()
print(cmd2.returncode) # 0
```

- Don't use `communicate` if you want to pipe the output of 1 command to another. 
	- Use communicate only when you want to get the output at the end.
- We can pass `cmd1.stdout` to `stdin` since `cmd1.stdout` is not string, it is a file.
	- If you want to read the contents of the file without using communicate you would need to use `readlines` or `readline`, Eg: `cmd2.stdout.readlines()`

### Using `run`
- **Without** `shell=True` we need to provide the command and its arguments. 
	- With `shell=True` we can run the same commands as we used to run in shell.

```python
import subprocess

subprocess.run("ls -alh", shell=True)

# or 
subprocess.run(["ls", "-alh"])
```

#### Getting stdout, stderr and return code

```python
import subprocess

output = subprocess.run(
    "echo hello",
    shell=True,
	check=True,
    stdout=subprocess.PIPE,
    stderr=subprocess.PIPE,
    universal_newlines=True, # gives us a string instead of bytes
)

print(output) 

# Output:
# CompletedProcess(args='echo hello', returncode=0, stdout='hello\n', stderr='')
```

- Values like stderr or stdout can be accessed using the dot notation.
- Adding `check=True` means that the command will raise an exception if the return code is not 0.
	- Instead of `check=True` as an argument we can also use `output.check_returncode()` on the next line. It will do the same thing.
- *We can also add a `timeout=5` parameter if we want to timeout long running commands after a certain command*.
- Instead of setting `stdout=subprocess.PIPE` and `stderr=subprocess.PIPE` we can use `capture_output=True` which does the same thing.
	- But if you need to direct stdout and stderr to different locations then `capture_output` won't be useful.
- Similarly we have `text=True` which can be used instead of `universal_newlines`

```python
import subprocess

cmd1 = subprocess.run("ls -alh", shell=True, capture_output=True, text=True)
print(cmd1.stdout)
```

#### Redirecting output to a file
```python
import subprocess

with open("output.txt", "w") as f:
    cmd1 = subprocess.run("ls -alh", shell=True, stdout=f)
```

#### Piping
```python
import subprocess

cmd1 = subprocess.run("ls", shell=True, capture_output=True, text=True)
cmd2 = subprocess.run(
    "wc -l", shell=True, capture_output=True, text=True, input=cmd1.stdout
)
print(cmd2.stdout)
```

- `input` is used for stdin.
	- `text=True` then a sequence of strings is passed otherwise a sequence of bytes.
- Piping is only useful if you are storing the output for further processing the above example can be easily simplified by using `ls | wc -l` directly in `subprocess.run`.

## References
- [Python Tutorial: Calling External Commands Using the Subprocess Module - YouTube](https://www.youtube.com/watch?v=2Fp1N6dof0Y)
- [How To Use subprocess to Run External Programs in Python 3 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-use-subprocess-to-run-external-programs-in-python-3)
- [Python Subprocess Module || Python For Beginners || Python Tutorial #16 - YouTube](https://www.youtube.com/watch?v=gu2jyJ6oIdk)