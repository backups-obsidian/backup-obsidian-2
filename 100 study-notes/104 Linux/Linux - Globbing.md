---
created: 2022-12-16 22:09
updated: 2022-12-16 22:31
---
---
**Links**: [[104 Linux Index]]

---
## File globbing
- **Bash DOES NOT support native regular expressions like some other standard programming languages**. 

> [!note]- The Bash shell feature that is used for matching or expanding specific types of patterns is called globbing. 

- Globbing is mainly used to match filenames or searching for content in a file.
	- Globbing uses wildcard characters to create the pattern.

### Symbols
- `?` - Used to match **any one** single character.
	- ![[attachments/Pasted image 20221216221229.png]]
- `*` - Used to match **zero or more** characters.
	- A simple `*` means everything.
	- ![[attachments/Pasted image 20221216221418.png]]
- `[]` - Used to **match a range** for a single character. 
	- All uppercase alphabets are defined by the range as `[A-Z]` .
	- All lowercase alphabets are defined by the range as `[a-z]`.
	- All numeric digits are defined by the range as `[0-9]`.
	- All uppercase and lower alphabets are defined by the range as `[a-zA-z]`.
	- All uppercase alphabets, lowercase alphabet and digits are defined by the range as `[a-zA-Z0-9]`
	- It is generally used in conjunction with `*`
		- ![[attachments/Pasted image 20221216221800.png]]

> [!important]- `**/*.py` will recursively traverse all subdirectories and find all files that end in `.py`

> [!tip]- Apart from linux we can use Python's `glob` module to recursively look for files in a sub directories.
> I can also be used to just list out all the files in subdirectories starting from a root directory.