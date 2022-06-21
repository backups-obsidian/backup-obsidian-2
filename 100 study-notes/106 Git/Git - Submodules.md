---
created: 2022-06-21 19:35
updated: 2022-06-21 19:47
---
---
**Links**: [[106 Git Index]]

---
## Submodules
- This is used when we want to *include libraries or 3rd party code* in our project repository.
- One way of doing it is downloading the code, copying the files into the project and then commit the new files.
- The *drawbacks* of the above method are:
	- We are *mixing external code with your own files*
	- Updating the external code is again a *manual process*
- This is a common problem and the solution to this is git submodules.
- **A submodule is a git repository within a git repository**.
- It is a good practice to place the submodules in folder like `lib` or `vendor` so that they are cleanly separated from our code.

> [!important]+ The **actual contents of the submodule are not stored within our repository**.
> - A parent repo only stores the *submodule's remote url*, *local path* inside the project and the *checked out revision*.
> - The submodule's working files are here in our project now since we want to use the libraries but they are not part of the parent repo.
> - When we clone a project with the default clone command the sub module folder remains empty.