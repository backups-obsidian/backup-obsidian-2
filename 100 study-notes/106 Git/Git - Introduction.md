---
created: 2022-06-11 15:43
updated: 2022-06-11 16:08
---
---
**Links**: [[106 Git Index]]

---
## Introduction
- Git makes the distinction between *three distinct areas/concepts*:

-   The **repository** (local repo in the diagram) itself, which is stored *within* the `.git` folder 
	- Everything that we commit to our repository will be stored within the `.git` folder
-   The **working tree** (also called **working directory**), which corresponds to the *current state of files on your filesystem*
-   The **staging area** (also called the **index** or **cache**), which is the area that you can use to *prepare commits / temporarily save your work*

![[attachments/Pasted image 20220611154109.png]]
![[attachments/Pasted image 20220611155506.png]]

- The working tree is the first one that you see when you look at a folder containing a git repository. 
	- The *working tree is what sits on your filesystem*. 
	- It *may contain exactly the same content* as a branch in your repository, *but it can also be completely different*, depending on the changes that you make to it.
	- ![[attachments/Pasted image 20220611154718.png]]

- Git is aware of everything that happens inside of the working tree, but it only tracks the files that it is told to track.
- When you work with git you will have many instances where, after adding new lines of code to your `.gitignore` file, the *ignored files still show up in your git commit staging area*.
	- When you experience such instances, the best way to resolve the issue is to *clear and clean your Git cache*.
	- `git rm --cached file`: removes the copy of the file from the index / staging-area, **without touching the working tree copy**.
	- To clear your entire cache and staging area, use the `git rm` command with the recursive `-r` option: `git rm --cached -r`

