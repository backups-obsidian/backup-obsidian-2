---
created: 2023-03-02 09:09
updated: 2023-03-10 07:59
---
---
**Links**: [[114 AWS SOA Index]]

---
## EC2 Launch Troubleshooting
### `InstanceLimitExceeded` 
- If you get this error, it means that you have reached your limit of *max number of vCPUs per region*.
- **On-Demand instance limits are set on a *per-region* basis**.
- Example: If you run on-demand (A, C, D, H, I, M, R, T, Z) instance types you’ll have 64 vCPUs (default) 
- **Resolution**: 
	- Either launch the instance in a different region or 
	- Request AWS to increase your limit of the region

 > [!note] vCPU-based *limits ONLY apply to* running **On-Demand** instances and **Spot instances**.

### `InsufficientInstanceCapacity`
- If you get this error, it means *AWS DOES NOT* have that *enough On-Demand capacity* in the **particular AZ** where the instance is launched. 
- Resolution: 
	- *Wait for few mins* before requesting again. 
	- If requesting more than 1 requests, *break down the requests*. If you need 5 instances, rather than a single request of 5, request one by one. 
	- If urgent, submit a *request for a different instance type* now, which can be *resized later*. 
	- Also, can *request* the EC2 instance in a **different AZ**.

### `InstanceTerminatesImmediately` 
- Goes from *pending to terminated*.
- Possible reasons:
	- You've *reached your EBS volume limit*. 
	- An *EBS snapshot is corrupt*. 
	- The root *EBS volume is encrypted* and you *DO NOT have permissions* to access the *KMS key for decryption*. 
	- The instance store-backed AMI that you used to launch the instance is missing a required part (an image.part.xx file). 
- To find the exact reason, check out the EC2 console of AWS instances - Description tab, note the reason next to the State transition reason label.

## SSH Troubleshooting
- Make sure the *private key file* on your linux machine has **400 permissions**, else you will get **Unprotected private key file** error.
- Make sure the username for the OS is given correctly when logging via SSH, else you will get *Host key not found*, *Permission denied*, *Too many authentication failures* or *Connection closed by instance port 22* error. 
- Possible reasons for **Connection timed out** to EC2 instance via SSH: 
	- *SG* is not configured correctly
	- *NACL* is not configured correctly
	- Check the *route table for the subnet* (routes traffic destined outside VPC to IGW)
	- Instance *doesn’t have a public IPv4*
	- CPU load of the instance is high

### How SSH instance connect works
- The EC2 instance will have an **inbound rule** in which we will **allow a specific range of IP** from AWS that corresponds to the **AWS EC2 Instance Connect range**.
	- Without this it won't work.
	- We probably never noticed this since we always had an SG which allowed port 22 access from all locations (`0.0.0.0/0`)
- SSH instance connect *pushes an one time public key*.
	- ![[attachments/Pasted image 20230302092410.png]]