---
created: 2022-06-22 19:42
updated: 2022-06-22 20:15
---
---
**Links**: 

- [[Git - Commands]]
- [[106 Git Index]]
---
## Branches
### Listing and Creating branches
- `git branch` : List out all the branches and the current branch you are on. 
	- Lists only the *local branches*.
- `git branch <branch-name>` : Creating a branch
	- `git branch <branch-name> <commit-hash>` : start our branch from a specific commit.
- `git checkout <branch-name>` or `git switch <branch-name>` : Moving to a branch
- `git checkout -b <branch-name>` : Creating and moving to a branch

### Deleting Branches
- You cannot delete the branch the HEAD is on.
- `git branch -d <branch-name>` : Deleting local branches
- `git branch -D <branch-name>` : To force delete a branch
	- Used when you have commits which are present nowhere else.
- `git push origin --delete <remote-branch>` : Deleting a remote branch

### Renaming Branches
- **Local branches**:
	- `git branch -m <new-name>` : Renaming the branch head is at.
	- `git branch -m <old-branch> <new-branch>` : Renaming the branch even though the head is not at it.
- **Remote Branches**:
	- We first delete the remote branch and then push the new local branch with correct name.
	- `git push origin --delete <name>` : Deleting the remote branch
	- `git push -u origin <name>` : Pushing the local branch

### Tracking Branches
- We need the `-u` option if we are publishing a branch for the first time.
- It tells git to establish a *tracking connection which means pushing and pulling will be much easier*.
- We can also *pull the branches present on remote which are not present in our local repository* using `git branch --track <new-branch> origin/<base-branch>`
	- Example: `git branch --track feature/login origin/feature/login` 
- This can also be done using `git checkout --track origin<base-branch>`
	- Example: `git checkout --track origin/feature/login` 
	- Here we only provide one argument which is the name of the remote branch. 
	- Git automatically creates the a local branch name `feature/login` for us.
- `git branch -v` : This can be **useful to know if the branches have diverged** 
	- That is there are some changes in our local branch that is not there in the remote branch and there are some changes in the remote branch that are not present in our branch

### Merging
- `git merge <branch-name>` : merging the `branch-name` to the branch you are on.
	- [[Git - Merging#Merging Examples|Git Merging Examples]]
	- Aborting the merge `git merge --abort`
- `git branch -f <branch-name> <commit/relative-ref/tag>` : Forcefully moving a branch pointer to another commit.
	- [[Git - Relative Refs#Example Moving Branches|Example Moving Branches]]

> [!note] We create branches in our local repository, branches are created in the remote repository by publishing the local branches.