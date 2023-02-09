---
created: 2022-05-30 12:29
updated: 2023-02-01 16:39
---
---
**Links**: [[102 AWS DVA Index]]

---
## KMS Limits
- When you exceed a request quota, you get a **ThrottlingException**
- For cryptographic operations (like encrypt, decrypt, GenerateDateKey etc) , they *share a quota*. 
	- This includes *requests made by AWS services on your behalf* (ex: SSE-KMS)
- How to deal with KMS limits:
	- Use *exponential backoff* (backoff and retry)
	- *For GenerateDataKey*, consider using *DEK caching* from the Encryption SDK
	- You can request a *Request Quotas increase through API or AWS support* 

## S3 & KMS
### Forcing S3 SSL and KMS
- To force SSL, create an S3 bucket policy with a **DENY on the condition** `aws:Secure Transport = false`
- Forcing encryption:
	- *Deny incorrect encryption header*: make sure it includes `aws:kms` (== SSE-KMS)
	- *Deny no encryption header* to ensure objects are not uploaded un-encrypted

### S3 Bucket Key
- New setting to **decrease** 
	- Number of API calls made to KMS from S3 by *99%*
	- Costs of overall KMS encryption with Amazon S3 by 99%
- This leverages *data keys*
	- A S3 bucket key is generated
	- That key is used to encrypt KMS objects with new data keys
	- ![[attachments/Pasted image 20220601120714.png]]
- You will see *less KMS CloudTrail events* in CloudTrail

> [!question]- Important question
> You have critical data stored in an S3 bucket with `SSE:KMS` encryption enabled. You're running an application on an EC2 instance in which you want to download some files from the bucket. So, you have created an IAM role with `s3:GetObject` permissions and attached it to the EC2 instance, but when the application tries to download files from the S3 bucket, it gets a *denied exception*. What is a possible cause for this issue?
>
> Add permission for `KMS:Decrypt`. Because the bucket encrypted using `SSE:KMS`, **you must give permissions to the EC2 instance to access the KMS Keys and to make decrypt operations**.

## CloudWatch Log Encryption
- You can encrypt CloudWatch logs with KMS keys
- **Encryption is enabled at the log group level**, by associating a CMK with a log group, *either when you create the log group or after it exists*.

> [!note]- You *CANNOT* associate a CMK with a log group using the CloudWatch *console*.

- You **must use** the CloudWatch Logs **API**:
	- **`associate-kms-key`** : if the *log group already exists*
	- **`create-log-group`** : if the *log group doesn't exist yet*

- Also for Logs to be encrypted the *key policy must be updated* so that CloudWatch logs service can use KMS to encrypt and decrypt the logs.
	- ![[attachments/Pasted image 20220601151248.png]]
	- This just means our key can be used by CloudWatch logs.
