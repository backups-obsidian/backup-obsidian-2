---
created: 2022-06-01 11:42
updated: 2023-02-24 15:07
---
---
**Links**: [[102 AWS DVA Index]]

---
## How does KMS work
### Basic Encrypt and Decrypt
- KMS *Encrypt and Decrypt API* call has a limit of **4 KB**
	- ![[attachments/Pasted image 20220601095331.png]]
	- We choose the CMK with which we want to encrypt the data, **KMS checks if we have the right permissions** and then performs the encryption for us.
- If you want to *encrypt > 4 KB*, we need to use **Envelope Encryption**

### Envelope encryption
> [!note] Envelope encryption is the **practice of encrypting plaintext data with a data key and then encrypting the data key under another key.**

- **Symmetric encryption** process.
- The main API that will help us is the **`GenerateDataKey`** API
- **Encryption Process**:
	- We call the `GenerateDataKey` API with the CMK with which we want to encrypt the data, *KMS checks if we have the appropriate permission*.
	- We will be sent a *plain text DataKey* and an *encrypted DataKey (cipher text)*
	- We encrypt the big file client side using the DataKey to get the encrypted file
	- We *store the encrypted data key and the encrypted data together* and *discard the unencrypted data key*.
	- ![[attachments/Pasted image 20220601112857.png]]
- **Decryption Process**:
	- We call the `Decrypt` API. 
	- Since we can only send 4KB of data to the `Decrypt` API so we send the encrypted DataKey.
	- KMS Decrypts the DataKey and sends it back to us. 
	- Now we can decrypt the file on client side.
	- ![[attachments/Pasted image 20220601113122.png]]

> [!note]- The whole encryption and decryption happens at *client side*.

#### Encryption SDK
- Envelope encryption is a cumbersome process so AWS provides an *Encryption SDK* that does Envelope Encryption for us.
- **Feature Data Key Caching**:
	- *Re-use data keys* instead of creating new ones for each encryption
	- Helps with *reducing the number of calls to KMS* with a **security trade-off**
	- Use `LocalCryptoMaterialsCache` (max age, max bytes, max number of messages)
		- The `LocalCryptoMaterialsCache` is implemented as a software component in the *AWS Encryption SDK*. 

### API Calls Summary
- *`Encrypt`*: encrypt up to *4 KB* of data through KMS
- **`GenerateDataKey`**: generates a unique *symmetric* data key (DEK)
	- Returns a *plaintext copy* of the data key
	- AND a copy that is *encrypted (ciphertext)* under the CMK that you specify
- **`GenerateDataKeyWithoutPlaintext`**:
	- Generate a DEK (data key) to use at some point (**not immediately**)
	- **DEK that is encrypted under the CMK** that you specify (**must use Decrypt later**)
		- This means we will need access to the `Decrypt` api.
	- To remember: *Longer means Later*.
- *`Decrypt`*: decrypt up to *4 KB* of data (**including Data Encryption Keys**)
- `GenerateRandom`: Returns a random byte string

> [!note] If you don't need to perform envelope encryption immediately then use *`GenerateDataKeyWithoutPlaintext`*

> [!important]- For encrypting EBS we need access to 2 APIs: `GenerateDataKeyWithoutPlaintext` and `Decrypt`

> [!question]- A development team has created a new IAM user that has `s3:putObject` permission to write to an S3 bucket. This S3 bucket uses server-side encryption with AWS KMS managed keys (SSE-KMS) as the default encryption. Using the access key ID and the secret access key of the IAM user, the application received an access denied error when calling the `PutObject` API.
> Correct the policy of the IAM user to allow the `kms:GenerateDataKey` action.

> [!question]- A new AWS Lambda function processes data and sends it to another service. The data is around *1 MB in size*. A developer has been asked to update the function so it *encrypts the data before sending it on to the other service*. Which API call is required to perform the encryption?
> Issue the AWS KMS **`GenerateDataKey`** API to return an encryption key

## SSE KMS Deep Dive
- SSE-KMS leverages the *`GenerateDataKey`* (*while uploading*) & *`Decrypt`* (*while downloading*) KMS API calls.
	- When we are uploading something S3 will make a `GenerateDataKey` API call to generate the data key and then encrypt whatever we are uploading. It will mostly store the encrypted datakey along with the object. While downloading it needs to send the unencrypted object back so it will make a `Decrypt` call to decrypt the encrypted data key and the perform the decryption.
	- These *KMS API calls will show up in CloudTrail*, helpful for logging
- To perform SSE-KMS, you need:
	- A KMS Key Policy that authorises the user / role
	- An IAM policy that authorises access to KMS
- **S3 calls to KMS for SSE-KMS count against your KMS limits**
	- If throttling, try exponential backoff
	- If throttling, you can request an increase in KMS limits

> [!important] If you are getting throttling issues in SSE-KMS then the issue is not with S3 but with the limit of KMS that is preventing us from encrypting or decrypting files.

## References
- [DEMO | AWS KMS | ENVELOPE ENCRYPTION - YouTube](https://www.youtube.com/watch?v=cE-0ANuOMaQ)