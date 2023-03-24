---
created: 2023-03-02 11:39
updated: 2023-03-22 08:47
---
---
**Links**: [[114 AWS SOA Index]]

---
## No Reboot
- Enables you to **create an AMI without shutting down your instance**.
- By **default**, it's **NOT selected** i.e. noreboot. 
	- *AWS will shut down the instance* before creating an AMI to *maintain the file system integrity of EBS*.
	- ![[attachments/Pasted image 20230302114108.png]]

### AWS Backup for AMI
- AWS Backup **DOESN'T reboot** the instances while taking EBS snapshots (*no-reboot behaviour*)
	- This won't help you to create an AMI that guarantees file system integrity since you need to reboot the instance
- To *maintain integrity  with automated backups* you need to provide the reboot parameter while taking images (EventBridge + Lambda + Createlmage API with reboot)
	- ![[attachments/Pasted image 20230302114332.png]]

## Sharing AMIs
- **You can only share AMIs that have unencrypted volumes and volumes that are encrypted with a customer-managed CMK**. 
	-  If you share an AMI with encrypted volumes, *you must also share any CMKs used to encrypt them*.
- **You do not need to share the Amazon EBS snapshots that an AMI references in order to share the AMI**
	- *Only the AMI itself needs to be shared*; the *system automatically provides the access to the referenced Amazon EBS snapshots* for the launch.
- You **CANNOT share an AMI that has volumes that are encrypted with an AWS-managed CMK**.
- AMIs are a *regional resource*. 
	- Therefore, *sharing an AMI makes it available in that Region*. 
	- To make an AMI available in a **different Region**, *copy the AMI to the Region and then share it*. 
		- **Sharing an AMI from different Regions is NOT available**.

## Cross Account AMI sharing
- We can share an AMI with another AWS account.
- Sharing an AMI *DOES NOT affect the ownership of the AMI*.
- You can *ONLY share AMls* 
	- That have **unencrypted volumes** or 
	- **Volumes that are encrypted with a customer managed key**. 
		- Since we can share a customer managed key and not an AWS managed key.
- If we share an *AMI with encrypted volumes*, you *must also share any customer managed keys* used to encrypt them.
	- ![[attachments/Pasted image 20230302114952.png]]

> [!note] Note we are talking about sharing keys for decrypting  *encrypted EBS volumes* in the shared AMI.

> [!question]- You have designed an AMI in an account that is optimizing the legacy database technology your gambling company has developed. You wish to share that AMI with other AWS accounts that belong to the same organization. How do you do it?
> *Edit the account list that can see the AMI from the AMI Console UI and the other accounts can start using it*. 
> ---
> - You can *share an AMI with specific AWS accounts without making the AMI public*. All you need is the AWS account IDs. 
> - You can only share AMIs that have *unencrypted volumes* and *volumes that are encrypted with a customer-managed CMK*. If you share an AMI with encrypted volumes, you must also share any CMKs used to encrypt them.

> [!question]- A SysOps Administrator has shared an AMI from account *A to account B* and then *de-registered the AMI in a few days*. What is the outcome of this action?
> - You can't launch new instances from the AMI in *account A or in account B*.
> - The instances *already launched* from the shared AMI in account B, are *not impacted* by this de-registration.
> ---
> Instances cannot be launched in either of the accounts with the de-registered AMI.

## Cross Account AMI Copy
- If you copy an AMI that has been shared with your account, *you are the owner of the target AMI in your account*.
- The **owner of the source AMI must grant you read permissions for the storage that backs the AMI** (EBS Snapshot).
	- In case of *cross account ami sharing no read permissions were needed*.
- If the shared AMI has encrypted snapshots, the *owner must share the key* or keys with you as well.
	- Can encrypt the AMI with your own CMK while copying. 
	- ![[attachments/Pasted image 20230302115431.png]]

## EC2 Image Builder
- Used to automate the **creation of Virtual Machines** or **container images**.
- **Automate the creation, maintain, validate and test EC2 AMIs**.
	- ![[attachments/Pasted image 20230302125349.png]]
- Can be run on a schedule (weekly, whenever packages are updated, etc...)
- Free service (only *pay for the underlying resources* like EC2 instances for validating and testing)
- It is regional service but we can *distribute the AMIs*.

## AMI in production
- You can force users to *only launch EC2 instances* from **pre-approved AMls** (*AMIs tagged with specific tags*) using **IAM policies**.
	- ![[attachments/Pasted image 20230302130114.png]]
- Combine with **AWS Config** to find *non-compliant EC2 instance* (instances launched with non-approved AMls)
	- ![[attachments/Pasted image 20230302130139.png]]

> [!question]- Your company has a critical application that's hosted on 100s of EC2 instances. The security team has created an AMI that's updated and has all the security patches installed. The DevOps team must create the EC2 instances from the AMI approved by the security team, but *there's no IAM policy to prevent them from using another AMI*. What *AWS service* would you use to ensure that all the EC2 instances are launched using the approved AMI?
> AWS Config