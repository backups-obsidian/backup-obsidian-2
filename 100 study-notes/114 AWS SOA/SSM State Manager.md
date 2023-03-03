---
created: 2023-03-02 15:33
updated: 2023-03-02 15:33
---
---
**Links**: [[114 AWS SOA Index]]

---
## SSM State Manager
- Automate the process of **keeping your managed instances (EC2/On-premises) in a state that you define**.
- Use cases: bootstrap instances with software, patch OS/software updates on a schedule, etc.
- State Manager **Association**:
	- *Defines the state* that you want to maintain to your managed instances
	- Example: port 22 must be closed, antivirus must be installed
	- *Specify a schedule when this configuration is applied*
- *Uses SSM Documents to create an Association* (e.g., SSM Document to configure CW Agent)