---
created: 2022-06-10 21:08
updated: 2022-06-21 20:00
---
---
**Links**: [[106 Git Index]]

---
## Tags
- Tags exist as anchors in the commit tree that designate certain spots.
- They are used permanently mark certain commits as milestones that you can then reference like a branch.

> [!note]- Tags are similar to branches, they only point to a specific commit but conceptually they are very different. Tags are meant to be permanent.
> To change a tag you have to do a force push.

- Initial:
	- ![[attachments/Pasted image 20220619193915.png]]
- `git tag v1 c1`
	- ![[attachments/Pasted image 20220619194002.png]]

### Describe
- Because tags serve as such great "anchors" in the codebase, git has a command to _describe_ where you are relative to the closest "anchor" (aka tag). And that command is called `git describe`!