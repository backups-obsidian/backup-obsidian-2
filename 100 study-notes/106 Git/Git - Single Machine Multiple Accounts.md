---
created: 2022-06-12 13:04
updated: 2022-06-12 13:04
---
---
**Links**: [[106 Git Index]]

---
- Create an config file in `~/.ssh`
- Sample file
```bash
host github.com
    HostName github.com
    User git
    IdentityFile ~/.ssh/github-keys/personal

host obsidian-backup
    HostName github.com
    User git
    IdentityFile ~/.ssh/github-keys/obsidian-backup


host project-cool
    HostName github.com
    User git
    IdentityFile ~/.ssh/github-keys/cool-project
```
- Generate key and upload the `.pub` part to GitHub accounts.

> [!question]- You would be thinking why can't I use the same public key for all the hosts?
> GitHub doesn't allow to you have the same public key across all the repositories globally

- Cloning personal projects : `git clone git@github.com:UserName/RepoName.git`
- Cloning project-cool projects : `git clone git@project-cool:UserName/RepoName.git`

- Use ssh for pushing instead of your gpg key authentication
```bash
git remote set-url origin git@project-cool:project-cool/obsidian-publish-action.git
git remote show origin
```