---
created: 2023-02-09 11:12
updated: 2023-02-09 11:12
---
---
**Links**: [[102 AWS DVA Index]]
**Keywords**: `get-session-token`

---
## Different types of MFA
- **SMS text message-based MFA**: SMS-based MFA is **available only for IAM users**, you **CANNOT use this type of MFA with the AWS account root user**.
- Hardware MFA device
- U2F security key
- Virtual MFA devices

- The above 3 MFA methods can be used with the root user.

### MFA with CLI
- You will first need to assign an MFA device by going to the security credentials section in IAM. 
- You will get an ARN of the MFA device after successfully adding it. This will be needed for making the API call.
- To use MFA with the CLI, you **must create a temporary session**.
- To do so, you must run the `aws sts get-session-token` API call.
	- ![[attachments/Pasted image 20220514162420.png]]
- If the API call is successful you will get an `AccessKeyId`, `SecretAccessKey` and a `SessionToken`. These are short lived and have an expiration.
	- ![[attachments/Pasted image 20220514163322.png]]

> [!important]- How to use the credentials from the `get-session-token` API call
> - We can configure a new aws profile with the `AccessKeyId` and `SecretAccessKey` we got just now. 
> - After configuring the profile paste the `SessionToken` in the credentials (`~/.aws/credentials`) file. 
> - The `AccessKeyId` and `SecretAccessKey` will already be there since we entered it while configuring the profile.
> ---
> ![[attachments/Pasted image 20220514163718.png]]

- Now any time you do a call using the above profile you will be having mfa access till the tokens expire.

> [!note] For exam we only need to know that we used **get-session-token**
