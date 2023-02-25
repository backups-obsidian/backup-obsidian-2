---
created: 2022-04-19 16:22
updated: 2023-02-24 11:23
---
---
**Links**: [[101 AWS SAA Index]]

---
## Encryption
- In *server side encryption* data is encrypted at the server side using the *data key*.
	- The encryption and decryption keys must be managed by KMS and the server must have access to it.
	- Data is decrypted before sending to us.

- In client side encryption data is always encrypted and decrypted by the client.
	- We can leverage **envelope encryption** for this.

## KMS
- Stands for Key Management Service. Anytime you hear encryption in AWS it will be KMS.
- AWS *manages the keys* for us.
- We can **fully manage the keys and its policies** like create, rotation, enable and disable.
- KMS is **fully integrated with IAM** for authorisation.
- AWS KMS is a *secure and resilient service* that uses hardware security modules that have been validated under **FIPS 140-2**.
- KMS seamlessly integrates with other AWS services which need data encryption like S3, EBS volumes, RDS etc.
- We can **audit** the key using **CloudTrail**. Which service has used the key.

- There are **4 types of CMKs**:
	- **AWS owned master key**: 
		- Collection of CMKs that an *AWS service owns and manages for use in multiple AWS accounts*. 
		- Although AWS owned CMKs are *not in your AWS account*, an AWS service can use its AWS owned CMKs to protect the resources in your account. Like *SSE-S3*.
		- Generally used if the user wants encryption and doesn't want to managed the keys.
	- **AWS Managed Service Default CMK**: *free*. 
		- Gets created during the **integration of AWS services**. Like the key you use in S3 SSE-KMS.
		- It rotates every 3 years.
		- They will start with `aws/` prefix
			- ![[attachments/Pasted image 20220531193937.png]]
	- **Customer Managed CMK**: *$1/Month*
		- We can only manage the customer managed CMK and not AWS managed default CMK.
	- **User Imported keys** (AES 256) (not recommended): *$1/month*

> [!question]- An application running on Amazon EC2 generates a large number of small files (1KB each) containing personally identifiable information that must be converted to ciphertext. The data will be stored on a proprietary network-attached file system. What is the SAFEST way to encrypt the data using AWS KMS?
> - Encrypt the data directly with a *customer managed customer master key*.
> - In this example the Amazon EC2 instance is saving files on a *proprietary network-attached file system* and this *will not have support for AWS managed CMKs*.

- Difference between the 3 keys
	- ![[attachments/Pasted image 20230209100836.png]]
	- Customer managed CMK provides the most flexibility.

- We *pay for API calls to KMS* 0.03$ / 10,000 calls.
- We can use KMS keys when we want to share sensitive information.
- The value in KMS is that the **CMK** used to encrypt data **can never be retrieved by the user** and **keys can be rotated for extra security**.
- KMS can only help in **encrypting upto 4KB** of data **per call**.
	- If **data > 4KB** we have to use **envelope encryption**.
    
- To give access to KMS to someone:
    - Make sure the **key policy allows the user**
    - Make sure **IAM policy allows API calls**.

- Keys are **bound to a specific region**.
	- To copy snapshots across accounts we need a [[KMS#^bd14fe|Custom Key Policy]]
	- *Steps* on how to copy a snapshot:
		- Create a Snapshot, encrypted with your CMK (either customer managed or AWS managed).
		- Attach a custom KMS Key Policy to authorise cross-account access
		- Share the encrypted snapshot
		- After decrypting the snapshot with the shared key (in target) create a copy of the Snapshot, 
		- Encrypt it with a KMS Key in your account
		- Create a volume from the snapshot

### KMS Key Policies
- These are **resource based policies** similar to bucket policies in S3. The *main difference is that we cannot control access without them*. 
	- If **no key policy is specified then no one can access them**. 

- **Default** KMS Key Policy
    - Created *automatically even if you don’t provide a specific KMS key policy*.
    - **Complete access to the root user = whole account**.
    - To give access to the key to any user we use IAM policies.

- **Custom** KMS Key Policy ^bd14fe
    - Define users, roles that can access the key.
    - Define who can administer the key
    - **Useful for cross account access** of you KMS key

> [!note]- Suppose you have an *AMI that has an encrypted EBS snapshot* using KMS CMK. You *want to share this AMI with another AWS account*. You have shared the AMI with the desired AWS account, but the other AWS account still can't use it. How would solve this problem?
> You need to share the KMS CMK used to encrypt the AMI with the other AWS account. A *custom key policy has to be used for cross account access*.

> [!caution]- Deleting a customer CMK
> - Deleting a customer master key (CMK) in AWS Key Management Service (AWS KMS) is *destructive and potentially dangerous*. Therefore, AWS KMS *enforces a waiting period*. 
> - To delete a CMK in AWS KMS *you schedule key deletion*. 
> - You can set the *waiting period from a minimum of 7 days up to a maximum of 30 days*. 
> - The *default* waiting period is *30 days*. 
> - During the waiting period, the CMK status and key state is Pending deletion. 
> - *To recover the CMK, you can cancel key deletion* before the waiting period ends. 
> - **After the waiting period ends you cannot cancel key deletion, and AWS KMS deletes the CMK**.
> - You *cannot recover the key after deletion*.

> [!note]- *AWS managed CMK CANNOT be deleted*, they can be **disabled**.

### KMS Key Rotation
- We can **enable key rotation for customer managed CMK** and **not for AWS managed CMK**.

#### Automatic rotation
- If **automatic key rotation** is enabled for customer CMK it will happen **every one year**. 
- In case customer CMKs are rotated *Previous key will be kept active* so that we can decrypt our old data.
- **New key** will have the **same CMK ID**. Only the **backing key is changed** every 1 year.
	- ![[attachments/Pasted image 20220429085011.png]]

#### Manual rotation
- When we want to rotate the key every *90, 180 days* etc
- **New key** will have a **different CMK ID**.
- Since the new key will have a different CMK ID we should use **aliases** to reference the new CMK to *hide the key change for application*.
	- ![[attachments/Pasted image 20220429085801.png]]

- **Previous key should be kept active** so that we can decrypt the old data.

> [!important] Good solution to rotate **CMK that are not eligible for automatic rotation like asymmetric CMK**.

## Types of CMK Keys
### Symmetric Keys
- **Single key** used to encrypt and decrypt
- They are necessary for *envelope encryption*
- When you generate a symmetric key with KMS you **never get access to the key unencrypted**. We must **use KMS API to use that key.**

> [!important] AWS services integrated with KMS *use symmetric CMKs*

### Asymmetric Keys
- We have **two keys** one *public* (for *encryption*) and one *private* (for *decryption*) key.
- They can be used for **encrypt/decrypt** or **sign/verify** operations.
- **Public key** is **downloadable** but we **cannot access the private key unencrypted**.

> [!important] Use case would be *encryption outside of AWS users who cannot call the KMS API*
