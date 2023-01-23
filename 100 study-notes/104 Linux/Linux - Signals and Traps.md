---
created: 2023-01-22 11:47
updated: 2023-01-22 17:17
---
---
**Links**: [[104 Linux Index]]

---
## Signals
- **Signals are software interrupts** sent to a program to indicate that an important event has occurred.
- It is limited form of inter process communication since we cannot transfer data using it.
- Signals are *delivered to the process by the kernel* either directly or indirectly.
	- Signals can originate from any where but in the end it is the kernel that delivers them.

- Listing all the signals: `kill -l`
- *Every signal has a value*. For example `SIGINT` has a value of 2.

> [!note]- All the signals have a value greater than 0.
> Success signal has a value of 0.

- **`SIGINT`**: *Interrupt* signal.
	- We can do this using `CTRL + C`
- **`SIGTSTP`**: *Stopping* the current process
	- We can do this using `CTRL + Z` for foreground processes
	- For background processes we use `kill -SIGTSTP <pid>`
	- If `SIGTSTP` doesn't work we can stop a 
- **`SIGKILL`**: *Kill* the current process
	- We can do this using `kill -9 <process-pid>`
	- We use `-9` since `SIGKILL` has a value of 9. 
		- This means `kill -9 <process-pid>` is same as `kill SIGKILL <process-pid>`
- **`SIGTERM`**: *Terminate* a process *gracefully*
	- We can do this using `kill -15 <process-pid>`
	- This is the *default signal* for `kill`.
- **`SIGHUP`**: A `SIGHUP` signal is sent to a process *when its controlling terminal is closed*.
	- `nohup` prevents the process from getting a `SIGHUP` signal.
	- `nohup sleep 1000`
- Some other signals
	- ![[attachments/Pasted image 20230122115232.png]]

> [!tip]- We can kill processes using process names instead of pid by using `pkill`.
> All the processes with the name will be killed.
> ![[attachments/Pasted image 20230122171446.png]]

> [!caution]- killing a parent process *DOES NOT* kill the children processes.
> - So interrupting a process using signals won't interrupt the subprocesses spawned by that process.
> - For example if there is a `sleep infinity` command in the bash script and we send a `SIGTERM` to the bash script process then the script will be killed but not the sleep command.
> - Though `SIGKILL` will kill the parent as well as the child processes.

> [!question]- Then why does `CTRL + C` kill parent and the child processes?
> - `Ctrl-C` at your terminal typically sends `SIGINT` to **all processes in the foreground process group**. 
> - Both your parent and your child process are in this process group.
> - This also means that if some process is in the background then it won't be stopped by `CTRL-C`
> - ![[attachments/Pasted image 20230122165947.png]]

- Difference between `SIGKILL` and `SIGTERM`
	- ![[attachments/Pasted image 20230122162208.png]]
	- Notice that `SIGTERM` kills all the child processes also.
	
- Killing a **process group** using `SIGTERM`: `kill -15 -<pid>`
	- ![[attachments/Pasted image 20230122165148.png]]
- Both foreground and background processes are removed when we send a `SIGTERM` to the process group.
	- ![[attachments/Pasted image 20230122170326.png]]

## Traps
- Signals can interrupt the execution of our script.
- Trap commands allow us to do some task (like clean up) whenever our script receives a signal.
- *Trap statements are always placed at the beginning of the script*.

```bash
#!/bin/bash

# specify the command to be executed on sigint
trap "echo interrupt signal detected" SIGINT

echo "Testing trap"
sleep 100

# ------
# Output
# Testing trap
# ^Cinterrupt signal detected
```

> [!caution]- Trap **CANNOT catch `SIGKILL`** and `SIGSTOP` command.

- We can catch multiple signals:
	- `trap "commands" SIGINT 0`
	- This is particularly useful in scenarios where you are writing to a file temporarily and would want to remove it incase the script is interrupted or script successfully completes execution.
		- `trap "rm -f $file; exit" 0 2 15`
		- *Clean up example*.

- We can use functions if we want to provide a lot of commands:
```bash
#!/bin/bash
function my_fun() {
	echo "inside trapped function"
	whoami
}
trap my_fun 15 2
```

- `trap "some commands" EXIT` gets called in case *program exits successfully or unsuccessfully*.
- You can also use **`trap`** to *ensure the user cannot interrupt the script execution*.
	- This feature is important when executing sensitive commands whose interruption may permanently damage the system. 
	- The syntax for disabling a signal is `trap "" SIGINT`. Double quotation marks mean that no command will be executed.
	- Remember `SIGKILL` cannot be trapped so this won't work in case user passed `SIGKILL`.

- Suppose a script is doing something and it is waiting for a specific process to complete and it receives as `SIGTERM` then the *trap won't be executed until and unless that process finishes*.

### Examples
- ![[attachments/Pasted image 20230122144250.png]]
- ![[attachments/Pasted image 20230122144430.png]]
- *Different Trap functions for different signals*.
	- ![[attachments/Pasted image 20230122162411.png]]

## References
- [signals and traps in shell scripting | trap command | ctrl + C on a shell script | trap handlers - YouTube](https://www.youtube.com/watch?v=HhGqMFU6W5k)
- [Bash trap Command Explained {With Examples} (phoenixnap.com)](https://phoenixnap.com/kb/bash-trap-command)
- [What is SIGTERM? What's the difference between SIGKILL & SIGTERM? (linuxhandbook.com)](https://linuxhandbook.com/sigterm-vs-sigkill/) 