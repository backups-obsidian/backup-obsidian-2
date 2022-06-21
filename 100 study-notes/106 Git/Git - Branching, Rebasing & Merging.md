---
created: 2022-06-09 22:14
updated: 2022-06-21 19:15
---
---
**Links**: [[106 Git Index]]

---
## Branching
- Branches in Git are incredibly lightweight as well. 
	- There is no storage / memory overhead with making many branches
- **Branches are simply pointers to a specific commit -- nothing more**. 
- This is why many Git enthusiasts chant the mantra:
> branch early, and branch often

> [!note] A branch essentially says **I want to include the work of this commit and all _parent commits_**

## [[Git - Merging | Merging]]

## Rebase
- The second way of **combining work between branches** is _rebasing._ 
- The advantage of rebasing is that it can be used to *make a nice linear sequence of commits*. 
	- The commit log / history of the repository will be a lot cleaner if only rebasing is allowed.

### Rebasing Examples
#### Example 1
- Initial state:
	- ![[attachments/Pasted image 20220609214541.png]]
	- we are on `bugFix`
- `git rebase main` : make `main` parent of `bugFix`
	- ![[attachments/Pasted image 20220609214611.png]]
- Rebasing from `main`
	- ![[attachments/Pasted image 20220609214754.png]]
- `git rebase bugFix` : making parent not possible, get me there.
	- ![[attachments/Pasted image 20220609214810.png]]
	- Since `main` was an ancestor of `bugFix`, git simply moved the `main` branch reference forward in history.

> [!caution]+ Notice that we have changed commits from `c2` to `c2'` and `c3` to `c3'` since Rebasing changes the commit history.
> Even if you try to reword a commit message it will change the whole history after that
>
> ![[attachments/Pasted image 20220621191437.png]]

#### Example 2
- Initial state:
	- ![[attachments/Pasted image 20220609222441.png]]
- `git checkout test; git rebase main`
	- ![[attachments/Pasted image 20220609222523.png]]
	- Note the dashes, your commit history has been changed

### Golden Rule of Rebasing
- Never use it on **public** branches.
- For example, think about what would happen if you rebased **master** onto your **feature** branch
	- ![[attachments/Pasted image 20220610200436.png]]
	- The rebase moves all of the commits in master onto the tip of the feature. The problem is that this only happened in your _repository_. All of the other developers are still working with the original master.

## HEAD
- **HEAD is the symbolic name for the currently checked out commit -- it's essentially what commit you're working on top of**.
- HEAD always points to the most recent commit which is reflected in the working tree. 
- *Most git commands which make changes to the working tree will start by changing HEAD*.
- **Detaching HEAD just means attaching it to a commit instead of a branch**. 
	- This is what it looks like beforehand: `HEAD -> main -> C1`
		- ![[attachments/Pasted image 20220609220909.png]]
	- And now it's: `HEAD -> C1`
		- ![[attachments/Pasted image 20220609220921.png]]

## Branching vs Rebasing
- Git Merge and Git Rebase are *both used to combine the changes of branches but in different ways*.
- You started working on a new feature in a dedicated branch, then the main branch is updated by other team members with new commits. This results in a *forked history*, which is very common in Git.
	- ![[attachments/Pasted image 20220610193858.png]]
- Now, if the **commits in the main branch are relevant to your feature branch** and you want to incorporate those commits into your feature branch. 
	- Right now you have the following two options:  **Merging or Rebasing**
- If we choose the Merge option then **The history of both the branches remain intact** and the existing branches are not changed in any way.

> [!note]- **Merge executes only one commit**
> ![[attachments/Pasted image 20220610194954.png]]
> Now your new feature branch will have all the latest changes of main
> Executed `git merge main` on the feature branch

- If we choose the Rebase option then the **entire feature branch is moved to begin on the tip of the main branch**, effectively *incorporating all the commits of the feature branch as new commits in the main branch*.
	- Behind the scenes, git is actually *blowing away the feature branch commits and duplicating them as new commits on top of the master branch*. 
	- What you get with this approach is a nice clean tree with all your commits laid out nicely in a row, like a timeline. Easy to trace.

> [!note]- Rebasing **re-writes** the project **history** by creating **brand new commits** for each commit in the original branch
> ![[attachments/Pasted image 20220610194342.png]]
> Notice the `*` this means the commits have been changed.

- Use Merge When
	- You want to preserve the *complete commit history and the chronological order*.
	- We want to add changes to a feature branch back into the main branch.
	- You want to revert the changes quickly.
- Use Rebase When
	- You want to squash multiple commits.
	- Maintain a *streamlined simple commit history*.