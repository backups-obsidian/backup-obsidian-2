---
created: 2022-05-16 12:39
updated: 2022-05-17 13:42
---
---
**Links**: [[102 AWS DVA Index]]

---
- Software packages depend on each other to be built (also called code dependencies), and new ones are created.
- *Storing and retrieving these dependencies* is called artifact management
- Traditionally you need to setup your own artifact management system 
- CodeArtifact is a **secure,** **scalable**, and **cost-effective artifact management** for software development
- Works with common dependency management tools such as Maven, Gradle, npm, yarn, twine, pip, and NuGet
- Developers and CodeBuild can then *retrieve dependencies straight from CodeArtifact*
- It can be used to **proxy requests** to other 3rd party artifact repositories. Advantages:
	- This provides security
	- Caching 
	- Even if the repository disappears from the public artifact repository you will have a copy of it.
- CodeBuild can directly fetch from the CodeArtifact.
- You can *publish your own artifacts* to the CodeArtifact.
- Everything is *inside your VPC*.

![[attachments/Pasted image 20220517134016.png]]