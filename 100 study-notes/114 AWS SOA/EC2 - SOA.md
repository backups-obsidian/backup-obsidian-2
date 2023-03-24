---
created: 2023-03-02 08:40
updated: 2023-03-24 08:47
---
---
**Links**: [[114 AWS SOA Index]]

---
## Enhanced Networking
![[../101 AWS SAA/HPC in AWS#Compute and Networking | Enhanced Networking]]
- If you want *enhanced networking for lower latency* then look for *ENA*  and if you have an *HPC cluster* then look for *EFA*. 
	- Enhanced networking is not supported in t2.micro but it is supported in t3.micro (newer generation)
	- So to *leverage enhanced networking* we have to leverage *newer generation of EC2 instances*.

## Termination Protection
- How should the instance react when the **shutdown is done from OS**.
	- *Stops (default)*
	- Terminate: CLI attribute is `InstanceInitiatedShutdownBehavior`
- The default shutdown behaviour can be changed when creating an EC2 in the advanced section.
	- If it is set to terminate then the EC2 instance will terminate when we issue a shutdown from OS.
	- ![[attachments/Pasted image 20230302090522.png]]
- This is not applicable when shutting down from AWS console. It only stops the instances.
- We can enable **termination protection** (`DisableApiTermination`) to protect against *accidental termination* from AWS **console or CLI**.
	- We *CANNOT enable termination protection for spot instances*.

> [!question]- We have an instance where shutdown behaviour = terminate and enable terminate protection is ticked. If we *shutdown the instance from the OS*, what will happen?
> The **instance will still be terminated** since we terminated it from the OS and not the console or CLI.
> **DOUBT**: I thought the default behaviour was to be stopped.

## Miscellaneous
- We can *change instance type* of a running instances ONLY IF they are **EBS backed**.
	- We first **stop** the instance, **change** the instance type and then **start** the instance.
	- AWS moves it to a new hardware but the *instance ID remains the same*.
- If your instance is in an *Auto Scaling group*, the Amazon EC2 Auto Scaling service marks the *stopped instance as unhealthy*, and *may terminate it* and launch a replacement instance.
	- To prevent this, you can **suspend the scaling processes for the group** while you're resizing your instance.

