---
created: 2022-06-10 19:25
updated: 2022-06-10 19:38
---
---
**Links**: [[106 Git Index]]

---
## Fetch vs Pull
- `git pull` does a `git fetch` followed by a `git merge`
	- ![[attachments/Pasted image 20220610192442.png]]
	
### Pull
- When you use `git pull`, git **tries to automatically merge**. 
	- `git pull` automatically merges the commits without letting you review them first. 
	- If you don’t carefully manage your branches, you may run into frequent conflicts.
- git will *merge any pulled commits **into the branch you are currently working** on*. 

### Fetch
- When you **do not want to directly merge the changes** from the remote repository and want to first review the code, then it is suggested to use git fetch.

## Fork (incomplete)
- Setting upstream

## Stash (commands incomplete)
- Scenario: Suppose you are working on a repository with two branches, A and B. The A and B branches have diverged from each other for quite some time and have different heads. *While working on some files in branch A, your team asks you to fix a bug in branch B*. You quickly save your changes to A and try to check out branch B with git checkout B.
- Git won't allow you to checkout B without stashing the changes you made in branch A.
- The `git stash` command **takes your uncommitted changes (both staged and unstaged)**, and saves them away for later use. 
	- The **stash is local to your Git repository**; stashes are not transferred to the server when you push.
	- Git temporarily saves your data safely without committing.