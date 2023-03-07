---
created: 2022-04-29 10:45
updated: 2023-03-06 15:46
---
---
**Links**: [[101 AWS SAA Index]]

---
## Inspector
- **Automated Security Assessments**
- For **EC2 instances**
	- Leveraging the *AWS System Manager (SSM) agent*. EC2 instances must have the SSM agent.
	- Analyse against unintended network accessibility
	- Analyse the *running OS against known vulnerabilities*
- For **Container Images pushed to Amazon ECR**
- For **Lambda Functions**
	- Identifies *software vulnerabilities in function code and package dependencies*.
	- Assessment of functions as they are deployed.
- Reporting & integration with *AWS Security Hub*.
- Send findings to *Amazon Event Bridge*.

> [!tip] Only for EC2 instances, Container Images & Lambda functions.

- **Package vulnerabilities** (*EC2, ECR & Lambda*) - *database of CVE*
- **Network reachability** (*EC2*)
	- Like port 22 is accessible from the internet.
- A risk score is associated with all vulnerabilities for prioritisation.

## Macie
- Amazon Macie is a **fully managed** data security and data privacy service that uses machine learning and pattern matching to discover and *protect your sensitive data in AWS*.
- **Discover sensitive data in S3 bucket.**
- **PII (Personally identifiable information)** is an important term to remember.