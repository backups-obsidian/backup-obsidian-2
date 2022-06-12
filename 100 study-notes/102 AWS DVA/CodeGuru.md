---
created: 2022-05-16 12:39
updated: 2022-05-17 13:50
---
---
**Links**: [[102 AWS DVA Index]]

---
- An *ML-powered* service for **automated code reviews** and **application performance recommendations**.
- Provides *two functionalities*
	- *CodeGuru Reviewer*: automated code reviews for **static code analysis** (**development**)
	- *CodeGuru Profiler*: visibility/recommendations about **application performance during runtime** (**production**)

![[attachments/Pasted image 20220517134602.png]]

## CodeGuru Reviewer
- Identify critical issues, *security vulnerabilities*, and hard-to-find bugs
- Example: common coding *best practices*, *resource leaks*, security detection, input validation
- Uses *Machine Learning* and automated reasoning
- Hard-learned lessons across millions of code reviews on 1000s of open-source and Amazon repositories
- Supports Java and Python
- Integrates with GitHub, Bitbucket, and AWS CodeCommit

## CodeGure Profiler
- Helps understand the *runtime behaviour* of your application
- Example: identify *if your application is consuming excessive CPU capacity* on a logging routine
- Features: 
	- Identify and remove code inefficiencies
	- Improve application performance (e.g., *reduce CPU utilisation*)
	- *Decrease compute costs*
	- Provides *heap summary* (identify which *objects using up memory*)
	- Anomaly Detection
- Support applications running on AWS or on premise
- *Minimal overhead* on application