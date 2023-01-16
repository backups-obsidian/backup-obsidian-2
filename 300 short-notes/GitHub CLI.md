---
created: 2023-01-14 18:33
updated: 2023-01-15 23:22
---
---
**Links**: [[300 home]]

---
## GitHub CLI
- Installing GitHub CLI: `brew install gh`
- Login using: `gh auth login`
	- Checking if you are logged in `gh auth status`
- Setting vscode as the default editor: `gh config set editor "code --wait"`
- Setting the git protocol: `gh config set git_protocol ssh`

- Useful repo commands:
	- `gh repo list`
	- `gh repo create <repo-name> --public -c`
- Creating gists: 
	- `gh gist create <file> --public`
	- `gh gist create <file1> <file2> --public` - create a gist with multiple files
	- `gh gist list`
	- `gh gist edit <id/url>`: we can get the id from the above command.

## References
- [New GitHub CLI Crash Course - First Look - YouTube](https://www.youtube.com/watch?v=BRAG1Kj4-Ss)