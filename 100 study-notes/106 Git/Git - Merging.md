---
created: 2022-06-18 16:05
updated: 2022-06-18 16:10
---
---
**Links**: 
- [[106 Git Index]]
- [[Git - Branching, Rebasing & Merging]]

---
## Git Merge
### Different types of merges
#### Fast Forward Merge
- A **fast-forward merge** can occur when there is a *linear path* between branches that you want to merge. 
- If the master branch has **not diverged**, then we we try to merge master with hotfix *instead of creating a new commit*, it will just point master to the latest commit of the hotfix branch. 
	- ![[attachments/Pasted image 20220610195559.png]]
- How it works
	- ![[attachments/Pasted image 20220618160655.png]]

#### Three Way Merge
- When there is not a linear path to the target branch, Git has no choice but to combine them via a three-way merge. 
- **This merge uses an extra commit** to tie together the two branches.
	- ![[attachments/Pasted image 20220610195846.png]]
- How it works
	- ![[attachments/Pasted image 20220618160834.png]] 
	- ![[attachments/Pasted image 20220618160900.png]]

### Merging Examples
> [!caution]- Note the arrow direction in these examples. 
> They point backwards which is correct hence the notion of parent.

#### Example: 2 Way merge
- Initial state:
	- ![[attachments/Pasted image 20220609222826.png]]
- `git checkout main; git merge test`
	- ![[attachments/Pasted image 20220609222902.png]]

#### Example: Three way merge
- Initial state:
	- ![[attachments/Pasted image 20220609213233.png]]
- Merge `bugFix` into `main` 
	- Notice we are on the `main` branch
	- This essentially means make `bugFix` parent of `main`
	- `git merge bugFix`
	- ![[attachments/Pasted image 20220609213327.png]]
	- If `c3` was not there and main was at `c1` then it would have directly moved to `c2` if we did `git merge bugFix` from `main` : `main` says making `bugFix` parent not possible so get me there.
- `main` now points to a *commit that has two parents*. If you follow the arrows up the commit tree from `main`, you will hit every commit along the way to the root. 
- This means that `main` contains all the work in the repository now.
- Merging `main` to `bugFix`: making parent not possible get me there.
	- `git checkout bugFix; git merge main`
	- ![[attachments/Pasted image 20220609214112.png]]

### Tips
> [!tip]+ In merge the (current branch : `branch-1`) `git merge branch-2`. This means make `branch-2` parent of `branch-1`.
> - Parent means behind
> - If making parent not possible then get me there.
> - `git merge my-parent` - for remembering

- In short if you want to merge your feature branch changes to main then `git checkout main; git merge feature`
- If you want to get all the changes made to main while you are working on your feature branch then `git checkout feature; git merge main`
	- We do this when we are working on something and we want the latest changes of main.

## Merge Conflicts
- Merge conflicts can occur while integrating commits from different sources
	- `git merge`, `git rebase`, `git pull`, `git stash apply`, `git cherry-pick`
- We have merge conflicts when two commits have different information on the same line
	- ![[attachments/Pasted image 20220618154953.png]]
- Undo a conflict and start over:
	- `git merge --abort`
	- `git rebase --abort`
- To resolve the merge conflict we just have to clean the file which will look something like this.
```txt
>>>>>>>> branch name
// changes
========
// changes
<<<<<<<< other branch
```
- We can specify a merge tool to be used when we have merge conflicts in git.