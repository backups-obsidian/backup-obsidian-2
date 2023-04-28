---
created: 2023-04-27 08:35
updated: 2023-04-28 08:17
---
---
**Links**: [[200 home]]

---
## Setup
- Install tmux using any package manager
- Install tmux package manager (TPM) by cloning it from the following link: [tmux-plugins/tpm: Tmux Plugin Manager (github.com)](https://github.com/tmux-plugins/tpm)
	- `git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm`
- We can create the tmux config file at either `$HOME/.tmux.conf` or `$XDG_CONFIG_HOME/tmux/tmux.conf` (`$HOME/.config/tmux/tmux.conf`)

## Configuration
- With the below configuration we will have a package manager for tmux and a sensible configuration to get started with.
```
set -g @plugin 'tmux-plugins/tpm'
Set -g @plugin 'tmux-plugins/tmux-sensible'

run '~/.tmux/plugins/tpm/tpm'
```

## Basic Idea
- Tmux consists of **3 mains objects: sessions, windows and panes**.
- **Sessions are the top most layer** in tmux and are a collection of one or more windows.
- We *can have any number of sessions open at a time but only attached to one*.
- Each session has a *single active window* (denoted by a \*).
	- ![[attachments/Pasted image 20230428080506.png]]
- **Windows are a container to one or more panes**.
- *Panes are split in the window and represent individual terminals*.
	- ![[attachments/Pasted image 20230428080712.png]]

## Shortcuts
| Shortcut            | Result                                                                                              |
| ------------------- | --------------------------------------------------------------------------------------------------- |
| Prefix + c          | Create a new window in a session                                                                    |
| Prefix + n/p        | To move between windows next/previous                                                               |
| Prefix + &          | Kill a window                                                                                       |
| Prefix + %          | Create a vertical pane                                                                              |
| Prefix + "          | Create a horizontal pane                                                                            |
| Prefix + arrow keys | Navigate panes                                                                                      |
| Prefix + q          | Panes have numbers which can be toggled using this. Pressing the number will switch us to that pane |
| Prefix + z          | Zooming into the pane to take whole window                                                          |
| Prefix + !          | Turn a pane into a window                                                                           |
| Prefix + x          | Close the pane. Just closing the shell will also work                                               |
| Prefix + s          | List tmux sessions                                                                                  |
| Prefix + w          | Preview windows for each session and attach to a session by pressing enter                          |

- Another way of killing the window is by killing all the panes inside it.

## Extra plugins
- Vim tmux navigator to navigate around panes using vim key bindings
	- `Set -g @plugin 'christoomey/vim-tmux-navigator'`

## Reference
- [Tmux has forever changed the way I write code. - YouTube](https://www.youtube.com/watch?v=DzNmUNvnB04)