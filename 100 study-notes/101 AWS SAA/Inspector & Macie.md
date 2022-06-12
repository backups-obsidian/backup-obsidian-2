---
created: 2022-04-29 10:45
updated: 2022-04-29 10:51
---
---
**Links**: [[101 AWS SAA Index]]

---
## Inspector
- **Automated Security Assessments for EC2 instances**.
- **Analyse the running OS** and **check it against known vulnerabilities**.
- Analyse against **unintended network activity**.
- For all these AWS inspector *must be installed on the OS of EC2 instances*.
- After the assessment the inspector generates a report and notifications can be sent via SNS.

> [!caution]- It is only for *EC2* instances. It does *network (agentless)* and *host assessments (agent)*
> If you see keywords like EC2, *OS with security* then go for inspector.

## Macie
- Amazon Macie is a **fully managed** data security and data privacy service that uses machine learning and pattern matching to discover and *protect your sensitive data in AWS*.
- **Discover sensitive data in S3 bucket.**
- **PII (Personally identifiable information)** is an important term to remember.