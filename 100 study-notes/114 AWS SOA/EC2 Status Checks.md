---
created: 2023-03-02 10:56
updated: 2023-03-02 11:15
---
---
**Links**: [[114 AWS SOA Index]]

---
> [!note] There can be 2 to 3 questions from status checks in the exam therefore it is important to understand the differences between system and instance status checks.

## EC2 Status Checks
- **Automated checks** to identify *hardware and software issues*.

### System Status
- Problems with the *underlying host*. 
- **AWS issue**.
- Some examples are: 
	- Loss of *network connectivity*
	- Loss of *system power*
	- *Software issues on the physical host*
	- *Hardware issues* on the physical host that impact network reachability
- **Resolution**: 
	- *Stop and start the instance* (*instance migrated to a new host*) or 
	- *Wait for AWS to fix it*

### Instance Status
- Instance status checks monitor the **software and network configuration** of _your individual instance_.
- Some examples are:
	- Incorrect *networking or startup configuration*
	- *Exhausted memory*
	- *Corrupted file* system
	- Incompatible kernel
- It *requires our intervention* to fix it.
- **Resolution**: 
	- *reboot the instance* or 
	- *change instance configuration*

### CW Metrics and Recovery
- CloudWatch Metrics (1 minute interval)
	- **`StatusCheckFailed_System`**
	- **`StatusCheckFailed_Instance`**
	- **`StatusCheckFailed`** (for both)
- **Option 1: CloudWatch Alarm** (**Preferred**)
	- *Recover EC2 instance* with the same private/public IP, EIP, metadata, and Placement Group.
	- Send notifications using SNS.
		- ![[attachments/Pasted image 20230302110400.png]]
- **Option 2: Auto Scaling Group**
	- Set *min/max/desired 1* to recover an instance but *won't keep the same private and elastic IP*.
		- ![[attachments/Pasted image 20230302110228.png]]