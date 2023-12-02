---
created: 2023-11-29 19:19
updated: 2023-12-01 10:22
---
---
**Links**: [[104 Linux Index]]

---
## Prerequisites
### Processes
- Every single process gets a **PID** and also a **directory in the `/proc` filesystem** when it is first created.
	- When it stops, its directory in the `/proc` filesystem is removed.
	- The directory given to the process is simply the process’s PID
		- So, for a process with the PID 1532, its directory to store its data is `/proc/1532`

### Everything is a file
- Imagine you are *a process and you want to send some data over the network*. 
	- The laptop/PC/server where you are running probably has some network adapters, sending a stream of bytes to these network adapters would send your file over the network. 
	- However, the network interface card may be manufactured by some particular manufacturer or adhere to some protocol and it may have defined its own standard way of taking input. You, as a process, probably shouldn’t care about that. 
		- If you had to, you’d probably have to do a lot of work to support Intel network interface cards, AMD network interface cards, etc. 
- What if you want to store data? 
	- How many different SSD makes would you have to support then? 
	- What if the system has an HDD instead of an SSD? 
	- Imagine writing all of this code in your programs that you made, a function to handle storing data in SSDs, one function to store data in Samsung HDDs, one in some other manufacturer’s HDD, etc.
- All this seems like a lot of work, and you as a process already have a lot of stuff to do.
- So, **Linux uses the concept of files for interoperability**. 
	- In short, *when you need to write data, you write data to a network socket or to a hard disk, or anywhere else as if it was a file*. 
	- Your *operating system will take this stream of bytes and make it available to whoever it was meant to go to*. 
	- The process in charge of receiving this stream of bytes will read them as if they were a file.
	- So *files become like a middleman* and *since it is a single standard, every process just needs to learn to read and write to files*. 
- Even when you need to read data from your keyboard or mouse, it is actually getting sent through a file. 
	- So, *when you press a key on your keyboard, Linux stores that text in some file*.
	- *Other processes can read this file*, and can thus find out what you are typing. 
	- This ensures that if you switched to a different keyboard, as long as the OS supports the keyboard, it would keep sending data to that same file, and your program never even finds out it's a different keyboard, it just keeps on reading the same file.

> [!note]- Summary
> - So treating everything as a file ensures interoperability between different processes. 
> - Every process can read from files and write to files, and *internally your operating system would map the stream of bytes to the correct process*. 
> - So if I am a process and I want to print a document, all I have to do is write the document as a file, and Linux would ensure that the printer’s drivers (also running as processes) would read from that file.

### Pseudo-terminals
- The are a bunch of files under `/dev/` directory that represents different types of terminals:
	- `/dev/tty*` – physical consoles;
	- `/dev/ttyS*` – serial connections;
	- *`/dev/pts/*` – pseudoterminals*.
- In order to determine what terminal file the current shell session is using, we have a `tty` cli.
	- ![[attachments/Pasted image 20231201102054.png]]

- **Pseudo-terminals are generally used to describe terminal emulators like gnome-shell for example**. 
- The pseudo-terminal can be *connected to a process and it can write or read data in the process*.
	- These *pseudo terminals can provide a way to send input to a process, and get output or error from a process*.
	- So, when you run a command, like `ls -la` (**process**) , the *output of the command is printed on your terminal*. 
		- This is because *by default the standard output, standard error, and the standard input all point to the terminal that started the process*.

> [!important]- Like everything else in Linux, *pseudo terminals are also files*. This means you can write to this file or read from it. These files are generally stored in the `/dev/pts/_N_` directory. Here _N_ is just a sequential identifier of the pseudo-terminal. Every terminal emulator you open creates a new file in the `/dev/pts` directory.
> Example
> - ![[attachments/Pasted image 20231130111350.png]]
> 
> So if I do another SSH login I will have 2 entries
> - ![[attachments/Pasted image 20231130111522.png]]
> 
> So every time you open a new pseudo terminal (like one from VSCode) it adds to one of the entries here.


> [!note]- Now we know that since these are files we can redirect output from one of them to other.
> - In this example I have 2 terminal sessions open. The location of the file for one of them is *`/dev/pts/3` (terminal 1)* and the other is *`/dev/pts/8` (terminal 2)*. 
> - So if I redirect output from terminal 1 to `/dev/pts/8` then I should see output in terminal 2.
> ![[attachments/Pasted image 20231130112537.png]]

> [!caution]- So, TLDR; even **pseudo terminals we open are technically files. If you need to write to a terminal, you just need to write to the `/dev/pts/_N_` file**.
> We can see this if we run a process. Its 0,1,2 would be pointing to the `/dev/pts/_N_` file unless specified.
> - ![[attachments/Pasted image 20231130113035.png]]
> - ![[attachments/Pasted image 20231130113230.png]]
>
> Since pseudo terminals are also files we can redirect the output of a process from our terminal to some other terminal.

### Devices
- Devices are also treated as files. 
	- For example, when I connect my USB mouse to my laptop, a few new files appear in my `/dev/` directory.
		- ![[attachments/Pasted image 20231130121556.png]] 
- Looking into these files further tells me they are of the type `character special`.
	- ![[attachments/Pasted image 20231130121614.png]]
- In essence, **`character` is a special file using which we can send or receive data from the physical device**, i.e. my mouse.

## File Descriptors
- A file descriptor is an **index into the file descriptor table**.
	- *Every process has a file descriptor table*.
	- The operating system also has a global file descriptor table.
	- The file descriptor table holds *pointers to resources* (files, terminal I/O, pipes, sockets, devices)
		- If a process uses them or opens them then they will have an entry in the file descriptor table.
		- By default we always have 3 file descriptors (0 -> stdin, 1 -> stdout, 2 -> stderr)
- So if you are a process, and you *want to access a file* (again, this file can be a socket, or a hardware device, or anything else really), all you need to do is *request a file descriptor* for it, and once you have that, you can use it to access the file.
- *All file descriptors that a process contains are stored in the directory `/proc/PID/fd`*.
- We can access file descriptor of any process using `/dev/fd`.
	- So if we want to direct some output to file descriptor 2 we use `echo "test" > /dev/fd/2`. This is same as doing `echo "test" 1>&2`

> [!important]- What do the file descriptors look like in case of redirection
> ![[attachments/Pasted image 20231130122313.png]]
>
> *From the process’s perspective, it is still writing to the file `1` when writing something to standard output*, but since the symlink now points to the file `output.txt` , it gets written to that file instead of the terminal. 
> This way *the process DOES NOT need to care about the fact that we are storing the output in the file, all it needs to do always is just to write to the `fd` 1*.

> [!question]- What happens when we open files using Python's `open()` function?
> ![[attachments/Pasted image 20231130122736.png]]
> 
> We can see that the **process (`python3`) has a new file descriptor now `3` which points to the file we just opened**.
> 
> *Closing the file releases the file descriptor as well*. This is why it is important to close files, especially when opening them in an iterative or recursive way. **Every process can open only a certain amount of file descriptors at most**, so not closing your files can exhaust the number of file descriptors you can open.
> ![[attachments/Pasted image 20231130122957.png]]

- **When we run any command on the terminal the process forks from the shell**.
	- The process **inherits all the descriptors** from the parent (in this case shell).
	- This is why a lot of commands (processes) run from the shell will have the same file descriptor as the shell running them.

- We have a per process file descriptor table. We also have a system wide file descriptor table.
	- The per process file descriptor table point to the system wide file descriptor table which in turn points to the inode table to get the path.
		- ![[attachments/Pasted image 20231201095409.png]]

### Understanding Sockets & Pipes
- When we run the command `sleep 1000000 | python3` we start 2 processes with PIDs x and y. In this example x is 520 and y is 521.
	- ![[attachments/Pasted image 20231130124303.png]]
	- `sleep 1000000` has its *output redirected to a pipe*, and the process with the PID 521, i.e. `python3` has its *input coming from a pipe*.
- The file `1` in process with PID 520 and the file `0` in the process with PID 521 both point to a PIPE *which are strings* instead of symbolic links to a file.
	- This string is definitely not a valid filename since it doesn’t even have a path.
- Running `python3 -m http.server` and checking its file descriptors also reveals something similar
	- ![[attachments/Pasted image 20231130124601.png]]
	- Here the difference is that instead of a pipe, we see a socket, but it is still a symbolic link to an invalid file name.
- The `socket:[SOME_NUMBER]` and `pipe:[SOME_NUMBER]` are simple strings that uniquely identify a particular socket or a particular pipe in your system. 
	- Anyone can read which socket or pipe the process points to very easily by checking where the symlink points to. 
	- So, *even though it is not a valid file*, it still contains _readable information about the pipe or the socket_. 
	- **The kernel does a bit of magic when handling pipes and sockets here and is just finding the target of the symbolic link** (which is the socket or the pipe ID), and using that directly instead of sending data to the file.

> [!question]- What happens if we try to redirect something to a file descriptor pointing to a socket or pipe?
> ![[attachments/Pasted image 20231130125204.png]]
> Understandably, the shell gives us an error that the path or the address is not valid.
> 
> So, the symlinks are not perfectly valid, but since the *kernel is the only one using the links* and the kernel is also the one who created these invalid links, it already knows the links are invalid and would not try to access the targets as file paths but would instead read the targets as metadata about pipes and sockets. 
> **This breaks the notion that "Everything is a file"**, since, as we just saw, *sockets and pipes are NOT really files*.

### Creating file descriptors
- We can create file descriptors using `exec`. To learn more about `exec` refer [[Linux - Use of exec]].
- Creating a file descriptor: `exec 3>&1`
	- If we do `echo 'test' >&3`  we will get test printed on the terminal
	- ![[attachments/Pasted image 20231130153303.png]]
- Closing a file descriptor: `exec 3>&-`
- Similarly we can create a file descriptor to write to a specific file: `exec 4 > newfile.txt`
	- We can write to it using `echo 'test' 1>&4`

#### Use Case: Selective logging
```bash
exec 3>&1 >>log/terminal.log 2>&1

echo "installing stuff..." >&3 # this goes to terminal
run_command something # this goes to logfile

echo "starting node..." >&3 # this goes to terminal
run_command something2 # this goes to logfile
```

- If you want the terminal output to go to the log as well, you can use `tee`

```bash
echo "starting node..." | tee -a /dev/fd/3 # this goes to terminal AND logfile (-a is for append mode)

# using the above tee command we are writing to both stdout (fd 1) and fd3. But since fd1 goes to file and fd3 goes to terminal we are writing to both terminal and file at the same time.

echo "starting node..." | tee -a log/terminal.log 1>&3 # same as above
```

- The `tee` command **reads from the standard input** and **writes to both standard output (fd 1) and one or more files at the same time**. 
	- `tee` is mostly used in combination with other commands through piping.

## References
- https://medium.com/geekculture/developer-diaries-processes-files-and-file-descriptors-in-linux-ebf007fb78f8

## Read Later
- https://biriukov.dev/docs/fd-pipe-session-terminal/0-sre-should-know-about-gnu-linux-shell-related-internals-file-descriptors-pipes-terminals-user-sessions-process-groups-and-daemons/