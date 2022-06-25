---
created: 2022-06-25 10:17
updated: 2022-06-25 11:12
---
---
**Links**: [[106 Git Index]]

---
## `.gitignore`

> [!note]- The `.gitignore` file specifies what **untracked** files Git should ignore.

- `.gitignore` is a *plain text file* in which each line contains a pattern for files or directories to ignore.
- Lines starting with a hash mark (`#`) are **comments** and are ignored. 
- *Empty lines can be used to improve the readability of the file* and to group related lines of patterns.

### Slash
- The slash symbol (`/`) represents a directory separator. 
- The slash at the beginning of a pattern is relative to the directory where the `.gitignore` resides. 
	- If the *pattern starts with a slash*, it matches *files and directories only in the repository root*.
	- If the *pattern doesn’t start with a slash*, it **matches files and directories in any directory or subdirectory**.
	- If the pattern *ends with a slash*, it **matches only directories**. 
- When a *directory is ignored, all of its files and subdirectories are also ignored*.

- Pattern example matches:
	- `/access.log` : `access.log`
	- `access.log` : `access.log`, `logs/access.log`, `var/logs/access.log`
	- `build/` : `build`, `test/build`

- Examples: 
	- Notice the `I` around the file/folder which means ignored. `UT` means untracked
	- Folders/files with the same name are also ignored recursively
		- ![[attachments/Pasted image 20220625110602.png]]
	- By giving a slash we are ignoring only folders
		- ![[attachments/Pasted image 20220625110650.png]]
	- By giving a slash at the beginning we are only looking for files in the current directory and not recursively
		- ![[attachments/Pasted image 20220625110922.png]]
	- Files are matches without forward slash
		- ![[attachments/Pasted image 20220625111004.png]]


### Wildcard symbols
- `*` - The asterisk symbol matches zero or more characters.
	- `*.log` : `error.log`, `logs/debug.log`, `build/logs/error.log`
	- The wildcard character (`*`) will match anything except for a slash. That is it **won't match directories**.
	- `Documentation/*.html` matches `Documentation/git.html` but not `Documentation/ppc/ppc.html` or `tools/perf/Documentation/perf.html`
- `**` - Two adjacent asterisk symbols match any file or zero or more directories. 
	- `logs/**` : Matches anything inside the `logs` directory.
- When *followed by a slash* (`/`), it matches only directories.
	- `**/build` : `var/build`, `pub/build`, `build`
	- `foo/**/bar` : `foo/bar`, `foo/a/bar`, `foo/a/b/c/bar`

### Negating Patterns
- A pattern that starts with an exclamation mark (`!`) negates (re-include) any file that is ignored by the previous pattern.

```bash
*.log
!error.log
```

- `error.log` or `logs/error.log` will not be ignored

### Multiple `.gitignore`
- A local `.gitignore` file is usually placed in the repository’s root directory. 
- However, you can create multiple `.gitignore` files in different subdirectories in your repository. 
- The patterns in the `.gitignore` files are matched relative to the directory where the file resides.

> [!important]- Patterns defined in the files that reside in lower-level directories (sub-directories) have precedence over those in higher-level directories.

## Ignoring a Previously Committed Files
- The *files in your working copy can be either tracked or untracked*.

> [!note]- To ignore a file that has been previously committed, you’ll need to unstage and remove the file from the index, and then add a rule for the file in `.gitignore`
> `git rm --cached filename`

- The `--cached` option **tells git not to delete the file from the working tree but only to remove it from the index**.
- To recursively remove a directory, use the `-r` option: `git rm -r --cached filename`
- If you want to **remove the file from both the index and local filesystem**, omit the `--cached` option.

> [!important]- When recursively deleting files, use the `-n` option that will perform a *dry run* and show you what files will be deleted
> `git rm -r -n directory`

## Debugging `.gitignore` files
- Sometimes it can be challenging to **determine why a specific file is being ignored**, especially when you are are using multiple `.gitignore` files or complex patterns. 
- This is where the `git check-ignore` command with the `-v` option, which tells git to display details about the matching pattern, comes handy.
	- For example, to check why the `www/yarn.lock` file is ignored you would run: `git check-ignore -v www/yarn.lock`
- The output shows the path to the `gitignore` file, the number of the matching line, and the actual pattern.
	- `www/.gitignore:31:/yarn.lock	www/yarn.lock`
- The command also accepts *more than one filename as arguments*, and the **file doesn’t have to exist in your working tree**.

> [!note]- Displaying all the ignored files
> `git status --ignored`

## Committing ignored files
- Let's say you have an exception you want to make for a file that's usually ignored, but for whatever reason this project in particular needs it. 
- In cases like this you have a few options:
	- Tell Git to _not_ ignore this file by prefixing the file name with a `!` in .gitignore, i.e. `!.env`. This will override any global ignore files or ignore files in parent directories.
	- Use the `--force` option (or `-f` flag) when staging your files, i.e. `git add .env --force`

## References
- [Ignoring Files and Directories in Git (.gitignore) | Linuxize](https://linuxize.com/post/gitignore-ignoring-files-in-git/)
- [Git - gitignore Documentation (git-scm.com)](https://git-scm.com/docs/gitignore)