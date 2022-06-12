---
created: 2022-06-10 21:08
updated: 2022-06-10 21:22
---
---
**Links**: [[106 Git Index]]

---
## Discarding local changes (unstaged and tracked)
- `git restore` is a new command that has been introduced in Git 2.23 together with `git switch`. 
- Their purposes are to simplify and separate the use cases of `git checkout` that does too many things.
- `git checkout` can be used to switch branches (and also to create a new branch before switching to it). This functionality has been extracted into `git switch`.
	- `git switch` cannot be used to go to specific commits. This can only be done by `git checkout`
- `git checkout` can also be used to restore files to the state they were on a specified commit. This functionality has been extracted into `git restore`.
- Discarding local changes can still be performed by `git checkout` but the new commands are easier to use and less confusing.

## Discarding commits
### Reset
#### Example: Hard Reset
- Initial state:
	- ![[attachments/Pasted image 20220610211031.png]]
- `git reset --hard HEAD~1` : **rewrites history**
	- ![[attachments/Pasted image 20220610211101.png]]

> [!note] Even in case of a hard reset the *commit is not deleted*

### Revert 
- While *resetting works great for local branches* on your own machine, its method of "rewriting history" doesn't work for remote branches that others are using.
- In order to reverse changes and _share_ those reversed changes with others, we need to use `git revert`.

#### Example 
- Initial state:
	- ![[attachments/Pasted image 20220610211251.png]]
- `git revert HEAD`:
	- ![[attachments/Pasted image 20220610211317.png]]
