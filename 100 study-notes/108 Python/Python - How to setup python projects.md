---
created: 2022-09-16 16:24
updated: 2022-09-16 23:55
---
---
**Links**: [[108 Python Index]]

---
## Using Pyenv
- It is used for managing multiple python versions.

### Installation
- `brew install pyenv` - installing pyenv
- Add the following to your `~/.zshrc` file
	- `eval "$(pyenv init -)"`

### Commands
- `pyenv versions` - List all the *python versions* that are available for pyenv
- List all the python versions that can be installed
	- `pyenv install -l`
	- `pyenv install -l | grep 3.9`
- `pyenv install 3.9.7` - Install a specific version of python
- `pyenv global 3.10.4` - use a python version for all the terminals
	- Now whenever you invoke `python`, `pip` etc., an executable from the Pyenv-provided 3.10.4 installation will be run instead of the system Python.
- `pyenv shell <version>` - select just for current shell session
- `pyenv local <version>` - automatically select whenever you are in the current directory (or its subdirectories)
	 - Creates a `.python-version` file
- Using `system` as a version name would reset the selection to your system-provided Python.
- Uninstalling python versions:
	- As time goes on, you will accumulate Python versions in your `$(pyenv root)/versions` directory.
	- To remove old Python versions, use `pyenv uninstall <versions>`.
	- Alternatively, you can simply `rm -rf` the directory of the version you want to remove. 
		- You can find the directory of a particular Python version with the `pyenv prefix` command, e.g. `pyenv prefix 2.6.8`. 

> [!note]- So first you will set the python version that you want to use using pyenv and then use `python -m venv <virtual-env-name>` to create a virtual environment.
> - pyenv doesn't create virtual envs, just installs python versions.
> - After selecting a specific version of pyenv create a virtual env using venv.

- [pyenv/pyenv: Simple Python version management (github.com)](https://github.com/pyenv/pyenv#set-up-your-shell-environment-for-pyenv)

## Using poetry
- `brew install poetry` - installing poetry
- All the important metadata is in `pyproject.toml`
- Poetry also maintains a lock file for dependency management.

### Commands
- `poetry new <project-name>` - Creating a new project:
- Instead of creating a new project, Poetry can be used to initialise a pre-populated directory. To interactively create a `pyproject.toml` file in directory `pre-existing-project`:
	- `cd pre-existing-project; poetry init`
-  `poetry run pytest` - Running tests
	- Uses the `pytest` that comes with `poetry`.
- `poetry install` - Install all the dependencies on cloning the project:
- `poetry shell` - Spawn a new shell and activate the virtual env:
- `poetry env use /full/path/to/python` - We can tell poetry which environment to use.
	- We can do this after creating the virtual environment using venv.
- `poetry env info` - *List the virtualenv and system python versions and locations*.
- Show installed packages:
	- `poetry show`
	- `poetry show --tree`

### Installing Dependencies
- Add them to `pyproject.toml` under `tool.poetry.dependencies`
- Easier is to use the add switch though:
	- `poetry add <dependency-name>`
	- `poetry add pendulum@^2.0.5`: for a *specific version*
	- from git: `poetry add git+https://github.com/sdispater/pendulum.git`
	- Remove a dependency: `poetry remove <dependency-name>`
- For **dev dependencies**:
	- `poetry add --dev pytest`
	- `poetry add -D pytest`

### Using Scripts
```toml
[tool.poetry.scripts]
my-script = "src.main:main"
```
- `poetry run my-script` - Running the main function of the `main.py` inside `src`.

## Sample folder structure
![[attachments/Pasted image 20220916234253.png]]

> [!caution]+ If you want to use absolute imports properly without hassle run the following command at the root of the project (inside the python-project folder)
> `export PYTHONPATH="$(pwd)"`
