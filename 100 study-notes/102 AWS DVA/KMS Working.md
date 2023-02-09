---
created: 2022-06-01 11:42
updated: 2023-02-09 09:25
---
---
**Links**: [[102 AWS DVA Index]]

---
## How does KMS work
### Basic Encrypt and Decrypt
- KMS *Encrypt and Decrypt API* call has a limit of **4 KB**
	- ![[attachments/Pasted image 20220601095331.png]]
	- We choose the CMK with which we want to encrypt the data, *KMS checks if we have the right permissions* and then performs the encryption for us.
- If you want to *encrypt > 4 KB*, we need to use **Envelope Encryption**

### Envelope encryption
> [!note] Envelope encryption is the **practice of encrypting plaintext data with a data key and then encrypting the data key under another key.**

- **Symmetric encryption** process.
- The main API that will help us is the **GenerateDataKey** API
- *Encryption Process*:
	- We call the GenerateDataKey API with the CMK with which we want to encrypt the data, *KMS checks if we have the appropriate permission*.
	- We will be sent a plain text DataKey and an encrypted DataKey
	- We encrypt the big file client side using the DataKey to get the encrypted file
	- We build an envelope around the encrypted file and put the encrypted version of DataKey inside it.
	- ![[attachments/Pasted image 20220601112857.png]]
- *Decryption Process*:
	- We call the Decrypt API. 
	- Since we can only send 4KB of data to the Decrypt API so we send the encrypted DataKey
	- KMS Decrypts the DataKey and sends it back to us. 
	- Now we can decrypt the file on client side.
	- ![[attachments/Pasted image 20220601113122.png]]

> [!note]- The whole encryption and decryption happens at client side

#### Encryption SDK
- Envelope encryption is a cumbersome process so AWS provides an Encryption SDK that does Envelope Encryption for us.
- **Feature Data Key Caching**:
	- *Re-use data keys* instead of creating new ones for each encryption
	- Helps with *reducing the number of calls to KMS* with a **security trade-off**
	- Use LocalCryptoMaterialsCache (max age, max bytes, max number of messages)

### API Calls Summary
- *Encrypt*: encrypt up to *4 KB* of data through KMS
- **GenerateDataKey**: generates a unique symmetric data key (DEK)
	- Returns a plaintext copy of the data key
	- AND a copy that is encrypted under the CMK that you specify
- **GenerateDataKeyWithoutPlaintext**:
	- Generate a DEK to use at some point (**not immediately**)
	- DEK that is encrypted under the CMK that you specify (must use Decrypt later)
- *Decrypt*: decrypt up to *4 KB* of data (including Data Encryption Keys)
- GenerateRandom: Returns a random byte string

> [!note] If you don't need to perform envelope encryption immediately then use *GenerateDataKeyWithoutPlaintext*

## SSE KMS Deep Dive
- SSE-KMS leverages the *GenerateDataKey* & *Decrypt* KMS API calls
- These KMS API calls will show up in CloudTrail, helpful for logging
- To perform SSE-KMS, you need:
	- A KMS Key Policy that authorises the user / role
	- An IAM policy that authorises access to KMS
- **S3 calls to KMS for SSE-KMS count against your KMS limits**
	- If throttling, try exponential backoff
	- If throttling, you can request an increase in KMS limits

> [!important] If you are getting throttling issues in SSE-KMS then the issue is not with S3 but with the limit of KMS that is preventing us from encrypting or decrypting files.
