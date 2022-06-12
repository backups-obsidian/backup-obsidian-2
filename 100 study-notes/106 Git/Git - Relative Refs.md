---
created: 2022-06-10 20:58
updated: 2022-06-10 21:08
---
---
**Links**: [[106 Git Index]]

---
## Relative Refs
- Specifying commits by their hash isn't the most convenient thing ever when we want to move around, which is why Git has relative refs.
- With relative refs, you *can start somewhere memorable* (like the branch `bugFix` or `head`) and work from there.
- Mostly used relative refs:
	- Moving *upwards one commit at a time* with `^`
	- Moving *upwards a number of times* with `~<num>`
		- Say you want to move a lot of levels up in the commit tree. It might be tedious to type `^` several times, so Git also has the tilde `~` operator.

### Examples
#### Example: Using `^`
- Initial state:
	- ![[attachments/Pasted image 20220610205230.png]]
- `git checkout main^`
	- ![[attachments/Pasted image 20220610205259.png]]
	- Notice the `head` moved and not the `main` branch
	- Move 1 step relative to `main`
- We can also move relative to `head`. Initial state
	- ![[attachments/Pasted image 20220610205438.png]]
- `git checkout C3; git checkout HEAD^; git checkout HEAD^; git checkout HEAD^`
	- ![[attachments/Pasted image 20220610205524.png]]

#### Example: Using `~`
- Initial state:
	- ![[attachments/Pasted image 20220610205854.png]]
- `git checkout HEAD~4`: **excludes the current commit**
	- ![[attachments/Pasted image 20220610205926.png]]

#### Example: Moving Branches
- This is a use case of relative refs.
- Initial state:
	- ![[attachments/Pasted image 20220610210144.png]]
- `git branch -f main HEAD~3`: `-f` means forceful
	- ![[attachments/Pasted image 20220610210252.png]]
