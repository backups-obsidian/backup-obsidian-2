---
created: 2022-10-16 10:02
updated: 2022-10-16 16:11
---
---
**Links**: [[300 home]]

---
> [!caution]- If you press `?` anywhere in GitHub you will get keyboard shortcuts.

## How to use GitHub issues
- **Once we create an issue it gets a unique number**.
- We can create milestones and then assign issues to milestones.

> [!note]- **Milestones** can be thought of as **epic**, **issues** can be thought of as **user stories** and maybe **task lists** in issues can be thought of as **tasks**.
> ![[attachments/Pasted image 20221016150810.png]]

- We can have issue templates in `.github/ISSUE_TEMPLATE` folder.
- We can also have *issue forms*, they are an *extension of templates*.
	- Issue forms are `yml` files whereas templates are `md` files.

### References
- [Do you know the best way to manage GitHub Issues? - YouTube](https://www.youtube.com/watch?v=OccRyzAS4Vc)
	- Using estimates
	- Templates
- [Introducing the NEW GitHub Issues: Table View, Issues Forms, Task Lists - YouTube](https://www.youtube.com/watch?v=MvyGcLg6AvI)

## How to use GitHub projects
- It is a **way to organise issues**.

### References
- [How to Use GitHub for Automated Kanban Project Management - YouTube](https://www.youtube.com/watch?v=YVFa5VljCDY)

## How to use GitHub releases
- **Major release**: 
	- The versions are incompatible with each other.
	- This means 1.0.0 won't be compatible with 2.0.0
- **Minor release**:
	- Adding new functionality in a backwards compatible manner.
	- 1.1.0 and 1.2.0 will be minor release.
- **Patch**:
	- Backward compatible bug fixes.

- Alpha and beta can be thought of as pre releases.
- **Releases are based on git tags**.

> [!important]- Generally in releases you will have *zipped up version of you source code* unless you have other binaries.
> So whenever you go to a code base in GitHub always look for releases section to download the most stable version.

- We can create a release from the UI.
- *We can automate this process of creating releases using GitHub actions and tags*.
	- So suppose we have stable version we tag that particular commit with `v0.1.0`
	- We can have a GitHub action which looks for tags starting with `v` and then publishes them as a release.

### References
- [How to Release Code With Github - YouTube](https://www.youtube.com/watch?v=Ob9llA_QhQY)
- [GitHub Actions can automate your releases from your git commit messages - conventional commits - YouTube](https://www.youtube.com/watch?v=fcHJZ4pMzBs)