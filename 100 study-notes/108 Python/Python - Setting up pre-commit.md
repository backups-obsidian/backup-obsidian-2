---
created: 2022-10-20 20:08
updated: 2022-10-20 20:30
---
---
**Links**: [[108 Python Index]]

---
## Some terminologies
- **Static type checker**: 
	- Check for *types in python*
	- `mypy`
	- `pyright` 
		- Pylance is a language server, which provides features like IntelliSense, logical linting (e.g., errors and warnings), code actions, code navigation, semantic colorization, etc.
		- *Pyright is the open-source type checker which Pylance (in Vscode) uses under the hood to provide its functionality*.

- **Linter**: 
	- A linter, is a tool that analyses source code to flag programming errors, bugs, stylistic errors, and suspicious constructs.
	- They can *detect your code mistakes*, notice *invalid code patterns* and find *elements that do not follow your conventions*.
	- `pylint`, `flake8`, `eslint` (for JS and TS)

- **Code formatter**: 
	- `autopep8`, `black`, `prettier` (JS and TS)

## Pre commit
- Pre commit is used to run pre commit hooks
- Installing pre-commit
	- `brew install pre-commit` or
	- `pip install pre-commit`
- Installing pre-commit to the repository. 
	- It copies the `pre-commit` python file to `./git/hooks` folder.
	- Now since python is present in every system, it can run on any system.
	- `pre-commit install`
- Run `pre-commit` against all files:
	- Not advisable since we only want pre-commit to run on the files which have been staged and are ready for committing.
- We make a file called `.pre-commit-config.yaml` and the repos which we want to use for pre-commit

> [!note]- There are some pre-commit hooks like code formatters which will first fail the commit if the code is not formatted and then format the code. Now we have to stage the formatted files again.

### References
- [Python Pre-Commit Hooks Setup in a single video! - YouTube](https://www.youtube.com/watch?v=Wmw-VGSjSNg)
- [pre-commit](https://pre-commit.com/)