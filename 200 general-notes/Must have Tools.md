---
created: 2022-06-03 18:58
updated: 2022-07-26 16:23
---
---
**Links**: [[200 home]]

---
### Trash
- Replaces the need of `rm -rf` to prevent accidental deletion of important files
```bash
brew install trash
```
- ZSH config
```bash
alias rm="echo dont use rm, use trash to move files and folders to trash"
```

### Ntfy
- [dschep/ntfy: 🖥️📱🔔 A utility for sending notifications, on demand and when commands finish. (github.com)](https://github.com/dschep/ntfy)
```bash
pip3 install ntfy
```
- Bash configuration
```bash
export AUTO_NTFY_DONE_LONGER_THAN=-L30
export AUTO_NTFY_DONE_UNFOCUSED_ONLY=-b
source '/Users/sarthaknarayan/Library/Application Support/ntfy/auto-ntfy-done.sh'
```
- Allow notifications in mac and make the banner as alert
- Add the following
```yml
---
title: Shell Notifications
```
- To the path you get when you run `ntfy -v send "hello there"`

### PDF Compressor
- [theeko74/pdfc: Simple python script to compress PDF (github.com)](https://github.com/theeko74/pdfc)