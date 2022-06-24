---
created: 2022-06-10 19:25
updated: 2022-06-23 19:32
---
---
**Links**: [[106 Git Index]]

---
## What happens when you do a `git clone`
- The first thing you may have noticed is that a new branch appeared in our local repository called `origin/main`. 
- This type of branch is called a _remote_ branch; remote branches have special properties because they serve a unique purpose.
- Remote branches reflect the _state_ of remote repositories (since you last talked to those remote repositories). 
	- They help you understand the difference between your local work and what work is public. 

> [!note]- Remote branches have the special property that when you check them out, you are put into detached `HEAD` mode. 
> Git does this on purpose because *you can't work on these branches directly*; you have to work elsewhere and then share your work with the remote (after which your remote branches will be updated).

- Initial: 
	- Dotted lines is the remote
	- ![[attachments/Pasted image 20220619194851.png]]
- `git checkout origin/main; git commit`
	- As you can see, git put us into detached `HEAD` mode and then did not update `origin/main` when we added a new commit. 
	- This is because **`origin/main` will only update when the remote updates**.
	- ![[attachments/Pasted image 20220619194938.png]]

## Fetch vs Pull
- `git pull` does a `git fetch` followed by a `git merge`
	- ![[attachments/Pasted image 20220610192442.png]]

### Fetch
- Fetching data from the remote repository
- When you **do not want to directly merge the changes** from the remote repository and want to first review the code, then it is suggested to use git fetch.
- Initial: 
	- ![[attachments/Pasted image 20220619195238.png]]
- `git fetch`
	- Notice that only `origin/main` moved, our local repo main is still at the same point
	- ![[attachments/Pasted image 20220619195308.png]]
	- Commits `C2` and `C3` were downloaded to our local repository, and our remote branch `origin/main` was updated to reflect this.

- `git fetch` performs two main steps, and two main steps only
	- Downloads the commits that the remote has but are missing from our local repository
	- Updates where our remote branches point (for instance, `origin/main`)

> [!note]- What fetch *doesn't do*
> - `git fetch` does not change anything about _your_ local state. 
> - It **will not update your `main` branch or change anything about how your file system looks right now**.
> - This is important to understand because a lot of developers think that running `git fetch` will make their local work reflect the state of the remote. 
> - It may download all the necessary data to do that, but it does _not_ actually change any of your local files. 
> - So at the end of the day, you can think of running `git fetch` as a *download step*.
	
### Pull
- When you use `git pull`, git **tries to automatically merge**. 
	- `git pull` automatically merges the commits without letting you review them first. 
	- If you don’t carefully manage your branches, you may run into frequent conflicts.
- git will *merge any pulled commits **into the branch you are currently working** on*. 
- Initial:
	- ![[attachments/Pasted image 20220619200014.png]]
- `git fetch; git merge origin/main`
	- We downloaded `C3` with a `fetch` and then merged in that work with `git merge o/main`. 
	- Now our `main` branch reflects the new work from the remote (in this case, named `origin`)
	- ![[attachments/Pasted image 20220619200124.png]]

> [!note] Above is essentially what `git pull` does.

## What happens when you do a `git push`
- Initial state:
	- ![[attachments/Pasted image 20220623190045.png]]
- `git push`: 
	- The remote received commit `C2`, the branch `main` on the remote was updated to point at `C2`, and our _own_ reflection of the remote (`o/main`) was updated as well.
	- ![[attachments/Pasted image 20220623190129.png]]

## Diverged state
- Imagine you clone a repository on Monday and start dabbling on a side feature. By Friday you are ready to publish your feature -- but oh no! Your coworkers have written a bunch of code during the week that's made your feature out of date (and obsolete). They've also published these commits to the shared remote repository, so now _your_ work is based on an _old_ version of the project that's no longer relevant.
- In this case, the command `git push` is ambiguous. If you run `git push`, should git change the remote repository back to what it was on Monday? Should it try to add your code in while not removing the new code? Or should it totally ignore your changes since they are totally out of date?
- Because there is so much ambiguity in this situation (where history has diverged), git doesn't allow you to `push` your changes. It actually forces you to incorporate the latest state of the remote before being able to share your work.
- Example problem
	- ![[attachments/Pasted image 20220623190444.png]]
	- `git push` fails because your most recent commit `C3` is based off of the remote at `C1`. The remote has since been updated to `C2` though, so git rejects your push.
- Solution: using rebase
	- Initial state: 
		- ![[attachments/Pasted image 20220623190659.png]]
	- `git fetch; git rebase origin/main; git push`
		- First we download the commit C2, now we rebase creating C3\` and then we push the changes.
		- ![[attachments/Pasted image 20220623190807.png]]
- Solution: using merge
	- `git fetch; git merge origin/main; git push`
		- ![[attachments/Pasted image 20220623191234.png]]

> [!note]- `git pull` is a shorthand for **fetch and merge** and `git pull --rebase` is a shorthand for **fetch and rebase**.

> [!important]+ In order to push new updates to the remote, all you need to do is _incorporate_ the latest changes from the remote. 
> That means you can either **rebase or merge** in the remote branch (e.g. `origin/main`).

## Fork (incomplete)
- Setting upstream

## Stash (commands incomplete)
- Scenario: Suppose you are working on a repository with two branches, A and B. The A and B branches have diverged from each other for quite some time and have different heads. *While working on some files in branch A, your team asks you to fix a bug in branch B*. You quickly save your changes to A and try to check out branch B with git checkout B.
- Git won't allow you to checkout B without stashing the changes you made in branch A.
- The `git stash` command **takes your uncommitted changes (both staged and unstaged)**, and saves them away for later use. 
	- The **stash is local to your Git repository**; stashes are not transferred to the server when you push.
	- Git temporarily saves your data safely without committing.