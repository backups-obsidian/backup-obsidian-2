---
created: 2023-01-21 18:50
updated: 2023-01-22 16:16
---
---
**Links**: [[104 Linux Index]]

---
## Basics
> [!note]- Whenever we run any command in a Bash shell, *a subshell is created by default*, and a new *child process* is spawned (*forked*) to execute the command. 

- When using **`exec`, however, the command following `exec` replaces the current shell**. 
	- This means *no subshell is created and the current process is replaced* with this new command.

- Example:

```bash
$ pstree -p
init(1)─┬─init(52)───bash(53)
        ├─init(78)───bash(79)───pstree(108)
        └─{init}(7)
$ echo $$
79
$ exec sleep 300
```

- We checked the *PID of the current shell* using echo `$$` (gives the PID of the current process) and `pstree` commands and then executed a sleep of 300 seconds using `exec`. 
- Let’s now switch to a different terminal to check the process list

```bash
$ ps -aef | grep $USER
user1       53    52  0 23:37 tty2     00:00:00 -bash
user1       79    78  0 23:39 tty1     00:00:00 sleep 300
user1      111    53  0 23:40 tty2     00:00:00 ps -aef
user1      112    53  0 23:40 tty2     00:00:00 grep --color=auto 
```
- As we can see, *PID 79, which was initially assigned to Bash, is now assigned to the `sleep` command*.
- We’ll also observe that *after 300 second* `sleep` finishes, the *session (terminal), which had PID 79, **exited** since the shell was replaced by `exec` command, and its execution has completed*.

## What is `exec`
- `exec` is a command with two very distinct behaviours, *depending on whether at least one argument is used with it, or no argument is used at all*. 
- If at least one argument is passed, the first one is taken as a command name and `exec` try to execute it as a command passing the remaining arguments, if any, to that command.
- If the command exists and is executable, it replaces the current shell. 
	- That means that if `exec` appears in a script, the **instructions following the `exec` call will never be executed**.
	- This is why we always see *`exec` at the end of the scripts*.
	- A *successful `exec` never returns*.
- If no argument is passed, `exec` is only used to redefine the current shell file descriptors.
	- *The shell continue after the `exec`, unlike with the previous case*, but the standard input, output, error or whatever file descriptor has been redirected take effect.

## Use of `exec`
- Let’s assume Bash is not the default shell of our Linux box. **Interestingly, using `exec` command, we can replace the default shell in memory with the Bash** shell by adding it to the user’s login profile:
	- `exec bash`
- We can reset all the environment variables for a clean run using the `-c` option
	- `exec -c printenv`
	- `printenv` command lists the environment variables, giving it as an argument to `exec` command here prints an empty output.
- Used in conjunction with the `find` command.
- `exec` is also often used as an optimisation. 
	- If a shell script is going to run some other program _as the last thing it does_, running the other program without `exec` means the shell has to sit there waiting for the other process to finish, doing nothing useful. 
	- Using `exec` means the shell hands directly off to the other program, and there's no extra useless process hanging out taking up resources.

### Usage within bash scripts
- We can call scripts or other programs within a script using `exec` to override the existing process in memory.
- This implementation is particularly **useful in cases when we don’t want to return to the main script once the sub-script or program is executed**

```bash
#! /bin/bash

while true
do
   echo "1. Disk Stats "
   echo "2. Send Evening Report "
   read Input
   case "$Input" in
      1) exec df -kh ;;
      2) exec /home/SendReport.sh  ;;
   esac
done
```

- If we didn't use `exec` we would have never exit from the while loop:
	- ![[attachments/Pasted image 20230121192602.png]] 

### Redirection
- If you run `exec > file` instead, then the redirection applies to the entire shell: Any output produced by the shell is written to `file` instead of to your terminal. For example here

```bash
bash-3.2$ bash
bash-3.2$ exec > file
bash-3.2$ date
bash-3.2$ exit
bash-3.2$ cat file
Thu 18 Sep 2014 23:56:25 CEST
```

- I exit my shell (so that the redirection no longer applies) and I see that `file` indeed contains the output of the `date` command I ran earlier.
- This can be used for logging in scripts

```bash
#! /bin/bash
script_log="/home/shubh/log_`date +%F`.log"
exec 1>>$script_log
exec 2>&1
datee
echo "Above command is wrong, error will be logged in log file"
date
echo "Output of correct date command will also be logged in log file, including these echo statements"
```
- Logging stdout and stderr to the same file.

### Using `$@` with `exec`
- It takes all the variables passed to the script and then passes it to the command.
```bash
#!/bin/bash

exec echo "$@" >some.log
```

- Output:
```bash
cat some.log
a b c d
```

#### Use of `$@` in docker `entrypoint.sh`
- Sample `entrypoint.sh` script

```bash
#!/bin/bash
set -e

... code ...

exec "$@"
```

- It basically takes any command line arguments passed to `entrypoint.sh` using `CMD` instruction in `Dockerfile` or while running the container and execs them as a command. 
	- The intention is basically "Do everything in this `.sh` script, then in the same shell run the command the user passes in on the command line or what is defined in the `Dockerfile`".
	- If you have an image with an entrypoint pointing to `entrypoint.sh`, and you run your container as `docker run my_image server start`, that *will translate to running `entrypoint.sh server start` in the container*. 
		- At the exec line `entrypoint.sh`, the shell running as pid 1 will *replace itself* with the command `server start`

> [!important]+ `exec "$@"` is **critical for signal handling**. 
> - Without using `exec`, the `server start` in the above example would run as another pid, and *after it exits, you would return to your shell script*. 
> - With a shell in pid 1, a **SIGTERM will be ignored by default**. The reason being processes running as PID1 usually behave different than when running _not_ as PID1. Many processes ignore `SIGTERM` when running as PID1.
> - That means the graceful stop signal (`SIGTERM`) that `docker stop` sends to your container, would never be received by the `server` process since it will be sent to shell first (PID1) and it will ignore it. 
> - After 10 seconds (by default), `docker stop` would *give up on the graceful shutdown* and send a SIGKILL that will force your app to exit, but with *potential data loss or closed network connections*, that app developers could have coded around if they received the signal. 
> - It also means your container will always take the 10 seconds to stop.

- More about [[Linux - Signals and Traps | Signals]]

## Summary
- Without `exec`, the parent shell process survives and waits for the child to exit. 
- With `exec`, the child process replaces the parent process entirely so when there's nothing for the parent to do after forking the child, I would consider `exec` slightly more precise/correct/efficient.
- **without exec**
	-   parent shell starts
	-   parent shell forks child
	    -   child runs
	    -   child exits
	-   parent shell exits

- **with exec**
	-   parent shell starts
	-   parent shell forks child, replaces itself with child
	-   child program runs taking over the shell's process
	-   child exits

## References
- [The Uses of the Exec Command in Shell Script | Baeldung on Linux](https://www.baeldung.com/linux/exec-command-in-shell-script)
- https://stackoverflow.com/a/48096779