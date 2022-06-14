---
created: 2022-06-14 15:50
updated: 2022-06-14 16:31
---
---
**Links**: [[104 Linux Index]]

---
> [!note]- It is not meant to replace `find` or have all its utilities. 
> It can be used in scenarios where you need to find things quickly
> It comes with *sensible defaults*

- `brew install fd`
- **Ignores hidden directories** and **files** by default and the patters from `.gitignore`
- By default finds both files and directories
- `fd PATTERN` == `find . -iname "*PATTERN*"`
	- By default **finds both directories and files**.
	- ![[attachments/Pasted image 20220614155325.png]]
- **Smart case**: the search is *case-insensitive by default*. 
	- It *switches to case-sensitive if the pattern contains an uppercase character*.
- **Regular expression**: 
```bash
fd '^x.*rc$' // beginning with x/X and ending with .*rc
X11/xinit/xinitrc
X11/xinit/xserverrc
```
- **Specifying a root directory**: Second argument
```bash
> fd passwd /etc
/etc/default/passwd
/etc/pam.d/passwd
/etc/passwd
```
- List Everything: `fd`
- Searching for a **file extension**
```bash
> fd -e md
CONTRIBUTING.md
README.md
```
- **Searching a specific file or folder**
```bash
> fd -g libc.so /usr
/usr/lib32/libc.so
/usr/lib/libc.so
```
- **Hidden and Ignore files**:
	- `-H` for hidden files
	- `-I` for `.gitignore`
```bash
> fd pre-commit
> fd -H pre-commit
.git/hooks/pre-commit.sample
```