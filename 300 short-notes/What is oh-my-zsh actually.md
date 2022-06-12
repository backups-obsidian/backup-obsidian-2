---
created: 2022-04-26 22:59
updated: 2022-04-26 23:13
---
---
**Links**: [[300 home]]

---
- zsh is a shell just like bash.
- *oh-my-zsh* makes it easy to setup zsh using the concept of themes and plugins.
- **Themes are nothing but prompts**. 
	- The theme that I use `powerlevel10k` is also a prompt and not a theme.
- **Plugins are nothing but shell scripts** sourced by oh-my-zsh when the shell is loaded.
- `.zshrc` is the file how you configure your zsh shell. If you take a close look at it you will find that it first sets up the plugins and themes in the beginning and then just sources an .sh file from the `.oh-my-zsh` folder.
- If you go to `~/.oh-my-zsh/custom/` then you will see a 2 folders. One is for themes and other is for plugins. You will only have the plugins and themes that you are using listed here. 
- The main problem is that when we talk about zsh we about how it is unseparable form `oh-my-zsh`
- zsh doesn't have a plugin framework so when you search how to install plugins in zsh it will take you to `oh-my-zsh`

> [!important]- `oh-my-zsh` is nothing but a glorified config file written in zsh or just aliases.
> If you have any doubts then go to `~/oh-my-zsh/plugins` and take a look at some of the plugins.