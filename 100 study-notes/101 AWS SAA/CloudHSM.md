---
created: 2022-04-19 16:22
updated: 2023-03-06 22:10
---
---
**Links**: [[101 AWS SAA Index]]

---
## CloudHSM
- It stands for **Hardware Security Module**.
- In cloud HSM **AWS provides us with dedicated encryption hardware** and **we are responsible for managing our keys**.
- Cloud HSM *supports both symmetric and asymmetric encryption keys*.
- Good option to use **SSE-C type of encryption** since we get the hardware.
- CloudHSM is used for key management and storage but **not distribution**.
- CloudHSM *clusters* are spread across **Multi AZ (HA)** which is great for high availability and durability.
- We have **auditing which is independent of cloud trail**.
- Attempting to log in as the administrator more than twice with the wrong password zeroizes your HSM appliance. When an HSM is *zeroized, all keys, certificates, and other data on the HSM is destroyed*.
- We have to use *CloudHSM client to establish a connection to CloudHSM* and we are going to manage the keys overall.
- CloudHSM software is used to 
	- Manage the *keys*
	- Manage the *users* and their permissions. In KMS we use IAM.

> [!caution] If you want a *managed service* for creating and controlling your encryption keys, but you *don't want or need to operate your own HSM*, consider using *AWS Key Management Service* over CloudHSM.

> [!note] If we want to *import an asymmetric key, we can only do it using CloudHSM*.

> [!important] *CloudHSM has MFA support* whereas KMS doesn't.

### Integration with AWS Services
- Through *integration with AWS KMS*
- Configure **KMS Custom Key Store with CloudHSM**
	- Example: EBS, S3, RDS, etc
	- ![[attachments/Pasted image 20230306220708.png]]