---
created: 2023-03-02 15:33
updated: 2023-03-02 18:46
---
---
**Links**: [[114 AWS SOA Index]]

---
## SSM Patch Manager
- Automates the process of *patching managed instances* 
- OS updates, applications updates, security updates, etc.
- Supports *both EC2 instances and on-premises servers*.
- Supports Linux, macOS, and Windows
- Patch **on-demand** or on a **schedule using Maintenance Windows**
- Scan instances and *generate patch compliance report* (missing patches)
	- Patch compliance report can be sent to S3
- **Patch Baseline**:
	- Defines *which patches should and shouldn't be installed on your instances*
	- Ability to create custom Patch Baselines (specify approved/rejected patches)
	- Patches *can be auto-approved* within days of their release
	- By *default*, install only **critical patches** and patches related to security
- **Patch Group**:
	- **Associate** a set of *instances with a specific Patch Baseline*
	- Example: create Patch Groups for different environments (dev, test, prod)
	- Instances should be defined with the **tag key Patch Group**
	- An instance **can only be in one Patch Group**
	- *Patch Group can be registered with only one Patch Baseline*.

- Example Patch Baseline and Patch Group:
	- ![[attachments/Pasted image 20230302184550.png]]