---
created: 2022-06-08 19:22
updated: 2022-06-10 22:11
---
---
**Links**: [[106 Git Index]]

---
## Staging
- `git add` : stage the changes
	- `git add <file-name>`
	- `git add .` : all the changes
	- `git add -A` : to stage all the changes
	- `git add -u` : only stage the files that were updated, it will not stage the files which were newly added.

### Discarding local changes (before staging) (tracked)
- `git restore` : **discard the changes before staging**. It only works if the files have been tracked previously.
	- `git restore .`
	- `git restore <file-name>`
- In older versions of git it was `git checkout -- <file-name>`. For more info read [[Git - Staging & Committing#Discarding local changes unstaged|Discarding local changes unstaged]]

### Discarding local changes (untracked)
- *If the file isn't tracked by git, it's not git's job to remove it*. Just use normal shell commands, like `rm` instead of `git rm`.
- If you want to delete **all** untracked files, you could do a `git clean`. 
- `git clean -n` : Dry run files
- `git clean -nd` : Dry run files and folders
- `git clean -f` : Remove untracked files
- `git clean -fd` : Remove untracked files and folders
- `git clean -fx` : To remove ignored and non ignored files

### Cancel Staged changes (before committing)
- `git restore --staged <file-name>` : Unstage the changes, i.e. remove them from the staging area.
- `git reset HEAD <file-name>` : older way of doing it.
- `git reset .`

## Committing
- `git commit` : open editor for typing the commit messages

### Cancelling local commits 
#### Using Revert
- `git revert` : This will open the default editor, now you can edit the default commit message or leave it as is. Save and close the file.
	- `git revert HEAD` : If you just want to cancel the last commit
	- We may *cancel any random commit in history, pointing out its hash value or tags*.
	- `git revert HEAD --no-edit` : No editor. Commit message of the reverted commit will be used.
- This **doesn't change the history so this technique can be applied to any commit** (however there may be conflicts). It is *safe to use even in public branches* of remote repositories.
	- Both original and cancelled commits are seen in the history of the branch
	- [[Git - Reversing Changes#Example|Revert Example]]

#### Using Reset (incomplete)
- **Use only in private branches** (branches in which no one else is working) since you will be modifying the history.
- `git reset --hard <tag/hash/relative-ref>` : this will move the branch you are on to that commit.
	- [[Git - Reversing Changes#Example Hard Reset|Hard Reset Example]]
- `git reset --mixed <tag/hash/relative-ref>` : *default*, moves the all the changes (from your `HEAD` to hash) to the **unstaged area**.
- `git reset --soft <tag/hash/relative-ref>` : moves the all the changes (from your `HEAD` to hash) to the **staging area**

> [!caution] Don't use hard reset

### Changing commits
- Amending the previous commit
```bash
git add file
git commit --amend -m "message"
```
- Or we can use interactive rebase

### Squashing commits
- *Any rebase operation will change the history so try to use in private branches only*.
- `git rebase -i HEAD~3` : interactive rebase of last 3 commits.
- Interactive rebase alters every commit after the one you specify
	- `git rebase -i HEAD` : No commits after HEAD so it does nothing
- When you squash from bottom it moves to the most recent pick in upward direction. Squashed up in pick from bottom.

> [!note]- Whenever you use `git log` it will show you commits from newest to oldest whereas interactive rebase shows commits from oldest to newest.
> ![[attachments/Pasted image 20220610220032.png]]

## History
- `git log`
- `git log --pretty=oneline`
- `git log --pretty=format:"%h %ad | %s%d [%an]" --graph --date=short` : most useful.
- `git --pretty=format:"%h %ad | %s%d [%an]" --graph --date=short --all` : The `--all` flag guarantees that we see all the branches. By default, only the current branch is displayed.

### Checking out older versions
- `git checkout <hash>`

## Tagging
- Generally used for **tagging versions**.
- `git tag <tag-name>` : to tag a specific commit
- If you want to *tag older commits* for reference and easy jumping then `git checkout <hash>`, `git tag <tag-name>`
	- We can easily jump between commits using tag-names `git checkout <tag-name>`
- `git tag`: to list all the tags
- `git tag -d <tag-name>` : to delete that tag

## Moving files
- `git mv file destination`
- By moving files with git, we notify git about two things
	- The file was deleted.
	- The `desitnation/file` file was created.
- Both facts are staged immediately and ready for a commit. Git status command reports the file has been moved.

## Branches
- `git branch` : List out all the branches and the current branch you are on.
- `git branch <branch-name>` : Creating a branch
- `git checkout <branch-name>` or `git switch <branch-name>` : Moving to a branch
- `git checkout -b <branch-name>` : Creating and moving to a branch
- `git branch -d <branch-name>` : Deleting local branches
	- `git branch -D <branch-name>` : To force delete a branch
- `git merge <branch-name>` : merging the `branch-name` to the branch you are on.
	- [[Git - Branching, Rebasing & Merging#Merging Examples|Merging Examples]]
- `git branch -f <branch-name> <commit/relative-ref/tag>` : Forcefully moving a branch pointer to another commit.
	- [[Git - Relative Refs#Example Moving Branches|Example Moving Branches]]

## Stashing

## Rebasing
- `git rebase <branch-name>`

## Pushing
- `git push -u origin` : push to the same branch as the local one

## Pulling

## Rough

- do soft, the other changes goes to unstaged area, add them and stash them.
	- another way of squashing commit: reset to the oldest after which you want the commits, the changes will be in unstaged area, add them, and now make the single commit.
	- or interactive rebase.
- Force push to remove a commit from upstream.
- Forking and updating
	- `git fetch --all --prune` : to fetch the upstream changes, what is upstream?
	- `git reset --hard upstream/main` : local will be synced, push this to update your repo also
	- or `git pull upstream main` and not push to your repo