---
created: 2022-06-10 21:08
updated: 2022-06-14 19:27
---
---
**Links**: [[106 Git Index]]

---
## Cherry-picking
- Cherry-pick a very straightforward way of saying that you would like to copy a series of commits below your current location (`HEAD`).
- Initial state:
	- ![[attachments/Pasted image 20220614190446.png]]
- `git cherry-pick C2 C4`	
	- ![[attachments/Pasted image 20220614190533.png]]
	- We wanted commits `C2` and `C4` and git plopped them down right below us. 
- Order matters in cherry picking
	- ![[attachments/Pasted image 20220614190717.png]]
- Git cherry-pick is great when you know which commits you want (_and_ you know their corresponding hashes)

## Interactive Rebasing
- Initial state:
	- ![[attachments/Pasted image 20220614191234.png]]
- `git rebase -i HEAD~4`
	- After some commit reordering
	- ![[attachments/Pasted image 20220614191330.png]]

## Use case example
- Problem Statement:
	- I'm trying to track down a bug but it is quite elusive. In order to aid in my detective work, I put in a few debug commands and a few print statements. All of these debugging/print statements are in their own commits. Finally I track down the bug, fix it, and rejoice!
	- Only problem is that I now need to get my `bugFix` back into the `main` branch. If I simply fast-forwarded `main`, then `main` would get all my debug statements which is undesirable. There has to be another way...
- In this example could have been done using either interactive rebasing or cherry-picking
	- ![[attachments/Pasted image 20220614192259.png]]
- Solution : `git checkout main; git cherry-pick c4`