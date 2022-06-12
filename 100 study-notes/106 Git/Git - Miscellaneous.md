---
created: 2022-06-08 20:07
updated: 2022-06-08 20:16
---
---
**Links**: [[106 Git Index]]

---
## Setting up Aliases
- We can add aliases in the `~/.gitconfig` file
- By default you will have your name and email there
- We can also define these aliases in our `.bashrc` file
```bash
[alias]
  co = checkout
  ci = commit
  st = status
  br = branch
  hist = log --pretty=format:\"%h %ad | %s%d [%an]\" --graph --date=short
  type = cat-file -t
  dump = cat-file -p
```

## Using a different user
- You will first need to change the username and email in `~/.gitconfig` file
```bash
# This is Git's per-user configuration file.
[user]
	name = Sarthak Narayan
	email = sarthak.narayang@gmail.com
# Please adapt and uncomment the following lines:
#	name = Sarthak Narayan
#	email = sarthaknarayan@Sarthaks-MacBook-Air.local
```
- Then after getting the token from your GitHub account you will need to to add it to `~/.gitcredentials` file
```bash
https://<user-name>:<key>@github.com
```
