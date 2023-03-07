---
created: 2023-03-03 09:25
updated: 2023-03-06 19:09
---
---
**Links**: [[114 AWS SOA Index]]

---
## Changing The KMS Key For An Encrypted EBS Volume
- We **CAN'T change the encryption keys used by an EBS volume**.
- We need to create an EBS snapshot from the volume and then create a new EBS volume and specify the new KMS key.
	- ![[attachments/Pasted image 20230306185027.png]]

## Sharing KMS Encrypted RDS DB Snapshots
- We can share RDS DB snapshots encrypted with KMS CMK with other accounts, but **must first share the KMS CMK with the target account using Key Policy**.
	- ![[attachments/Pasted image 20230306185250.png]]

## Key Deletion
- **Schedule CMK for deletion with a waiting period of 7 to 30 days**.
	- CMK's status is "*Pending deletion*" during the waiting period
- During the CMK's deletion waiting period:
	- The **CMK CAN'T be used for cryptographic operations** (e.g., can't decrypt KMS-encrypted objects in S3 - SSE-KMS)
	- The key is *not rotated* even if planned
- You **can cancel the key deletion during the waiting period**. 
- Consider *disabling* your key *instead of deleting* it if you're not sure!

### Key Deletion - CloudWatch Alarm
- Using (CloudTrail -> CloudWatch Logs -> CloudWatch Alarms -> SNS) we can be notified when someone tries to use a CMK that's "*Pending deletion*" in a cryptographic operation (Encrypt, Decrypt, etc).
	- ![[attachments/Pasted image 20230306190754.png]]