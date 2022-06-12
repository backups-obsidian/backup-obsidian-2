---
created: 2022-05-29 20:23
updated: 2022-05-29 20:24
---
## Neovim Configuration (vim)
- Creating the config folder: `mkdir -p .config/nvim`
- Config file: `~/.config/nvim/init.vim`. This is equivalent to `.vimrc` file in vim.

## Plugins
- For using plugins we will have to use a plugin manager. [junegunn/vim-plug: Minimalist Vim Plugin Manager (github.com)](https://github.com/junegunn/vim-plug)

### Vim plug installation
```bash
mkdir -p ~/.config/nvim/autoload
curl https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim -o ~/.config/nvim/autoload/plug.vim
```

- We can also install it using the sh script given in the readme of the GitHub repository but it adds the `plug.vim`script to `~/.local/share`. I prefer to keep it in the nvim folder so that it is easy to sync using GitHub.

### Adding plugins using vim plug
```vim
" specifying the location for storing all the plugins
" this directory will be created by vim plug
call plug#begin('~/.config/nvim/plugged') 

" use single quotes only
Plug 'github link of plugin' 

call plug#end()
```
- We put all the plugins between plug begin and end.
- To install the plugin we need to do `:PlugInstall`
- *Don't commit the plugged directory to GitHub*. So whenever you clone your settings to a new system and do a `:PlugInstall` it will download the latest versions of the plugin.

### Useful Plugins
- `vim-fugitive` : for git integration
- `coc` : completion
	- You will have to do a specific `coc-intall` for the language server you want.

## Tips
- Once the plugin manager is set you can use tab for autocompletion while typing out commands.
- Use a particular theme: `:colorscheme gruvbox`