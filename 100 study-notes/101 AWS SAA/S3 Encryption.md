---
created: 2022-04-19 16:22
updated: 2023-02-25 15:19
---
---
**Links**: [[101 AWS SAA Index]]

---
## Encryption
- You are uploading sensitive data to S3 and you wouldn’t want your data to be compromised if someone gets into the AWS server.
- **Encryption is for a specific version ID**. So if the object was not encrypted and you upload an object with the same name and encryption enabled then only the latest version of the object will have encryption and not the previous ones.
- Encryption is of type **AES - 256**
- You can *enable default encryption on the bucket level* so all the objects uploaded to it will be encrypted.
- For encryption in transit AWS exposes HTTP and HTTPS (using TLS/SSL) endpoint.
- There are 4 ways of encrypting objects in S3.
- *Summary*
	- ![[attachments/Pasted image 20220423094258.png]]

### SSE - S3
- Keys are **handled** and **managed** by **S3**
- Objects are encrypted on the **server side**.
- To use this you must use the following **header**: `“x-amz-server-side-encryption”:”AES256”`
- You have no control over the keys or any kind of audit trail.

### SSE - KMS
- Keys are **handled** and **managed** by AWS [[KMS]].
- We can use the following *keys*:
	- KMS managed keys
	- KMS CMK (Customer Master Keys)
	- CMK can be customer generated

- KMS gives us **more control** and an *audit trail*. We can rotate the keys if we want.
- Objects are encrypted on the **server side**.
- To use this you must **use** the following **header**: `“x-amz-server-side-encryption”:”aws:kms”`
- There's also an *optional* `x-amz-server-side-encryption-aws-kms-key-id` header which *specifies the ID of the AWS KMS master encryption key* that was used for the object.
	- If the *header is not present* in the request, Amazon S3 assumes the *default KMS key*.

> [!tip]- When to go for SSE KMS over SSE S3
>  We should go for SSE - KMS if the company does not want to spend time managing and rotating the keys, *but it does want to control who can access those keys*.
> ---
> If you see terms like *audit trail*, *envelope encryption*, *automatic key rotation*. 

> [!caution] **Metadata**, which can be included with the object, is **not encrypted by any encryption method** while being stored on Amazon S3. Therefore, AWS recommends that *customers not place sensitive information in Amazon S3 metadata*.

### SSE - C
- Keys are **handled** and **managed** by **customer outside** of AWS
	- Use SSE-C over SSE-KMS when customer wants to have *control over the keys*.
- Objects are encrypted on the **server side**.
- AWS *doesn’t store the encryption key*.
- **HTTPS is mandatory**.
- **Encryption key** must be **provided** in the **HTTPS headers** for **every request**.
- Since you are managing the keys you would need to provide the key when you are requesting and file from S3 so that AWS can decrypt the object
- To use it we have to use **AWS CLI**, **AWS SDK** or **AWS S3 rest endpoint**. We **cannot use** the **web client**.
- Headers used for providing key information: `x-amz-server-side-encryption-customer-algorithm`, `x-amz-server-side-encryption-customer-key`, `x-amz-server-side-encryption-customer-key-MD5`
- The customer must provide the encryption keys, no other keys can be used.

### Client Side Encryption
- You **encrypt** the object **before uploading** it to S3.
- When you **fetch** something from S3 you must also **decrypt** it on the **client side**.
- In this case the **customer manages the keys** and the **encryption cycle**.
- Client side encryption can happen with the following *keys*:
	- Client managed keys
	- **KMS CMK**

> [!question]- When client side encryption is the right choice
> Both the *master keys* and the *unencrypted data* should never be sent to AWS to comply with the strict compliance and regulatory requirements of the company.
> In this case we use *Client side encryption with client side master key*. If question had only mentioned unencrypted data as the requirement then we could have used Client side encryption with KMS CMK.

> [!note] There is no auditing when we use client side encryption with client keys. If the question mentions auditing then always go for KMS key options.