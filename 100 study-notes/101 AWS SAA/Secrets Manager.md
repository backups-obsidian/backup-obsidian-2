---
created: 2022-04-19 16:22
updated: 2022-06-01 15:01
---
---
**Links**: [[101 AWS SAA Index]]
**Recommended Reads**: [[../102 AWS DVA/SSM Parameter Store vs Secrets Manager|SSM Parameter Store vs Secrets Manager]]

---
- It is a *newer service* and **came out after SSM parameter store**
- The **sole purpose is to store secrets**.
- We have the **capability to force rotation of secrets every X days**. This cannot be done with [[SSM Parameter Store]].
	- Automatic rotation is done with the help of a *lambda function*.
	- 
- *Automate the generation of secrets* on rotation (uses lambda)
- Secrets are **encrypted using KMS**
- **Integration with AWS RDS**.

> [!important] Mostly meant for secrets *RDS integration and rotation*

> [!caution] Whenever you see the word **rotated** with secrets always go with Secrets Manager over SSM Parameter Store.
