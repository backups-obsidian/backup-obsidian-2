---
created: 2022-05-16 12:39
updated: 2022-05-27 16:19
---
---
**Links**: [[102 AWS DVA Index]]

---
## CDK
- Stands for **Cloud Development Kit**.
- Define your cloud infrastructure using a familiar language:
	- JavaScript/TypeScript, Python, Java, and .NET
	- ![[attachments/Pasted image 20220527161406.png]]
- Contains high level components called constructs
- The code is **compiled into a CloudFormation template** (JSON/YAML)
	- ![[attachments/Pasted image 20220527161431.png]]
	- It is a step above CloudFormation and way easier to work with
	- Since the code is compiled we can find out errors if any before generating the template
- You can therefore *deploy infrastructure and application runtime code together*
	- Great for Lambda functions
	- Great for Docker containers in ECS / EKS

> [!note] In CDK there is something known as **constructs**

### CDK vs SAM
- *SAM*:
	- **Serverless focused**
	- Write your template declaratively In JSON or YAML
	- Great for quickly getting started with Lambda
	- Leverages CloudFormation
- *CDK*:
	- **All AWS services**
	- Write infra in a programming language JavaScript/TypeScript, Python, Java, and .NET
	- Leverages CloudFormation

