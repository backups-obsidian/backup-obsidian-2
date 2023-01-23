---
created: 2023-01-21 19:12
updated: 2023-01-22 17:37
---
---
**Links**: [[104 Linux Index]]

---
## Miscellaneous
### `$` commands
- `$$`: Get the PID of the current process.
- `$?`: Get the return code of the last executed command.  
- `$#`: Get the number of arguments in `$*`.
- `$*`: Get the list of arguments passed to the current process.

### Process Tree
- We can view the process tree of any command by first getting its PID and then using `pstree`: `pstree -p <pid>`
- Get all the process with pid: `pstree -p`
- Linear hierarchy: `pstree -ps <pid>`