---
created: 2023-03-02 15:33
updated: 2023-03-13 09:02
---
---
**Links**: [[114 AWS SOA Index]]

---
## SSM Patch Manager
- Automates the process of *patching managed instances* 
- OS updates, applications updates, security updates, etc.
- Supports *both EC2 instances and on-premises servers*.
- Supports Linux, macOS, and Windows
- Patch **on-demand** or on a schedule using **Maintenance Windows**
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
	- Instances should be defined with the **tag key `Patch Group`**
	- An instance **can only be in one Patch Group**
	- *Patch Group can be registered with only one Patch Baseline*.

> [!tip] 1 instance 1 patch group. 1 patch group 1 baseline.

- Example Patch Baseline and Patch Group:
	- ![[attachments/Pasted image 20230302184550.png]]

> [!question]- A security vulnerability has been discovered that impacts a version of Linux that is running on some Amazon EC2 instances in a VPC. How can a SysOps Administrator *mitigate the exposure with the LEAST disruption*?
> Use AWS Systems Manager to patch the Linux operating systems.

