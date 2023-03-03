---
created: 2022-04-20 20:42
updated: 2023-03-03 09:09
---
---
**Links**: [[101 AWS SAA Index]]

---

## Termination Policy
- Termination policy decides which instance will be deleted in case of a scale in.

- Default termination policy **steps**: 
	- Find the **AZ with the most number of instances**.
	- Find instances that are not protected from scale in.
	- If there are multiple instances delete the one with the **oldest launch template or configuration**.
	- If still there are multiple instances delete the one **closest to the next billing hour**.
	- If still there are multiple instances then **delete one at random**.

## Life cycle hooks
- We have one hook at the time of launching and other at the time of termination.
	- ![[attachments/Pasted image 20220420204629.png]]

- One use case of **launch hook** would be to do some **installations and checks** before the machine is provisioned. 
	- Here **installations and checks** are the important terms.
- One use case of **termination hook** would be **storing the logs** or **uploading it somewhere**.
- **By default** as soon as an instance is launched in ASG it is **InService**.
- Lifecycle hooks are integrated with *EventBridge*, *SNS* and *SQS*.

> [!info]-  The lifecycle hook puts the instance into a **wait state** (`Pending: Wait` or `Terminating: Wait`). The instance is *paused until you continue or the timeout period ends*.
> Example use case: Use the Auto Scaling group lifecycle hook to put the instance in a wait state and launch a custom script that installs the proprietary forensic tools and performs a pre-activation status check

> [!question]- A company has an ASG where random EC2 instances suddenly crashed in the past month. They *can't troubleshoot why the EC2 instances crash as the ASG terminates the unhealthy EC2 instances and replaces them with new EC2 instances*. What will you do to troubleshoot the issue and prevent unhealthy EC2 instances from being terminated by the ASG?
> Use **ASG Lifecycle Hooks** to *pause the EC2 instance in the Terminating state* for troubleshooting.