---
created: 2022-09-13 23:57
updated: 2022-10-02 12:26
---
---
**Links**: 
- [[../104 Linux/104 Linux Index | 104 Linux Index]]
- [[111 KodeCloud Index]]

---
## Package Management
- We can install packages using `rpm` 
	- But it won't install any dependent libraries. 
	- Like `rpm -i ansible.rpm` won't install python which is a requirement for ansible.

- `yum` is a high level package manager that uses `rpm` underneath
- It queries the remote repository for the package, installs all the dependencies as well as the package itself.
	- ![[attachments/Pasted image 20220913235843.png]]
- The information about the remote repositories is present at `/etc/yum.repos.d`
	- Every OS comes bundled with its own set of repositories from which we can install common software and tools.
- But at times the default set of repositories may not come with the software we are looking for or may contain an outdated version. 
	- *In this case we need to configure additional repositories so that `yum` can find these packages*.

- Get the list of repositories
	- `yum repolist`
	- ![[attachments/Pasted image 20220914000315.png]]
- If we visit the above URL we can see the `rpm` files that `yum` will eventually download and install.
	- ![[attachments/Pasted image 20220914000412.png]]
- Generally instructions for configuring a new repo is present with the software you are trying to install.
	- We can configure new repos using the `yum install` command
	- ![[attachments/Pasted image 20220914000543.png]]

- See the list of available or installed packages 
	- `yum list <package-name>`
	- ![[attachments/Pasted image 20220914000634.png]]
- We can list all the available versions of the package using 
	- `yum --showduplicates list ansible`
	- ![[attachments/Pasted image 20220914000714.png]]
- We can install a specific version using
	- `yum install <package-name>-<version>`
- Remove a package from ansible 
	- `yum remove <package-name>`