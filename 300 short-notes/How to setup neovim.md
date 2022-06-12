---
created: 2022-05-09 19:06
updated: 2022-06-01 00:06
---
---
**Links**: [[300 home]]

---
## Installing neovim
### Linux
```bash
cd ~/Downloads
curl -L https://github.com/neovim/neovim/releases/latest/download/nvim.appimage -o nvim
chmod u+x nvim

# doing ./nvim should start a nvim 
./nvim

# which nvim won't work since we haven't added it to path
# adding nvim to path
sudo mv nvim /usr/local/bin
```
- Don't use apt for installing neovim since the versions are pretty old.
- Best part of using app images is that you don't need dependencies everything is packaged into the app image.

### Mac
```bash
brew install neovim
```

- [Installing Neovim · neovim/neovim Wiki (github.com)](https://github.com/neovim/neovim/wiki/Installing-Neovim)

## Zshrc configuration
```bash
alias vim='nvim'

# if applications like github try to editor then it will default to nvim
export EDITOR='nvim' 
```

## Neovim configuration (lua)
- We can use *modules* in lua which this means we will not end up with large configuration files.
- We use the `require` function to load the different modules in lua. 
- So the base directory will have a `init.lua` file which will load all the modules.
- All the modules must be inside the `~/.config/nvim/lua` folder. For example we can have a settings folder inside lua folder. We will need a `init.lua` file in all the subfolders.

## Options
- `:help options`
- [Nvim documentation: options (neovim.io)](https://neovim.io/doc/user/options.html)

## Plugins 
- We will be using packer for our plugins
- Packer is also written in lua
- Plugins are just GitHub repositories
- Plugins will live in `~/.local/share/nvim`
- If you feel there is some problem then delete the `~/.config/nvim/plugin/packer_compiled.lua` file
	- Put it in your `.gitignore`
- Lazy loading means it will only run if one of the specified commands is run
- Plugins will be under `~/.local/share/nvim/site/pack/packer` - most of them will be in start

## ColourSchemes
- For colour schemes we have to first install them in the plugins and then use them

## Miscellaneous
- `:so %` after saving the file to source it.
- `echo &ft` - to get the file type

## LSP (incomplete)
- `LspInstallInfo` - a menu will pop up
- To install something search it in the menu and press `i`
- To update `u`
- To remove `X`
- To which particular one you have active in the current buffer `LspInfo`
- Any server must be installed for it to be active
- More to do
- null ls video

## Telescope
- find_files
- live_grep - `brew install ripgrep`
- Go to normal mode and mode up and down, go to insert mode to type

## Commenting 
- For better commenting use `nvim-ts-context-commentstring` (generally for webdev languages)

## Toggle term
- When you scroll you go to visual mode, use a/i to go to insert mode