---
created: 2022-04-19 16:22
updated: 2023-03-07 08:06
---
---
**Links**: [[101 AWS SAA Index]]
**Recommended Reads**: [[../102 AWS DVA/SSM Parameter Store vs Secrets Manager|SSM Parameter Store vs Secrets Manager]]

---
## Secrets Manager
- It is a *newer service* and **came out after SSM parameter store**
- The **sole purpose is to store secrets**.
- We have the **capability to force rotation of secrets every X days**. This cannot be done with [[SSM Parameter Store]].
	- Automatic rotation is done with the help of a *lambda function*.
- *Automate the generation of secrets* on rotation (uses lambda)
- Secrets are **encrypted using KMS**
- **Integration with AWS RDS**.

> [!important] Mostly meant for secrets *RDS integration and rotation*

> [!caution] Whenever you see the word **rotated** with secrets always go with Secrets Manager over SSM Parameter Store.

## Secrets Manager Monitoring (SOA)
- CloudTrail captures API **calls to the Secrets Manager API** and **other related events** that might have a security or compliance impact on your AWS account or might help you troubleshoot operational problems.
- CloudTrail records these events as non-API service events:
	- RotationStarted event
	- RotationSucceeded event
	- *RotationFailed event*
	- *RotationAbandoned event* - a manual change to a secret instead of automated rotation
	- StartSecretVersionDelete event
	- CancelSecretVersionDelete event
	- EndSecretVersionDelete event
- *Combine with CloudWatch Logs and CloudWatch alarms for automations*.
	- ![[attachments/Pasted image 20230307080502.png]]
- **Rotation failed events are important**.

- Troubleshooting rotation:
	- ![[attachments/Pasted image 20230307080444.png]]