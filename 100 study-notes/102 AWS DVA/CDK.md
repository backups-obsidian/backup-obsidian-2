---
created: 2022-05-16 12:39
updated: 2023-03-01 18:22
---
---
**Links**: [[102 AWS DVA Index]]

---
## CDK
- Stands for **Cloud Development Kit**.
- Define your cloud infrastructure using a familiar **language**:
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

### Different layers of constructs
#### L1
- Can be called **CFN Resources** which represents all resources directly available in CloudFormation.
- Construct names start with **`Cfn`** (e.g., `CfnBucket`)
- You must *explicitly configure all resource properties*.
	- ![[attachments/Pasted image 20230301181820.png]]

#### L2
- Represents AWS resources but with a *higher level* (**intent-based** API)
- Similar functionality as L1 but with *convenient defaults and boilerplate*
	- You don't need to know all the details about the resource properties
	- ![[attachments/Pasted image 20230301181931.png]]

#### L3 
- Can be called **Patterns**, which represents multiple related resources.
- Helps you *complete common tasks in AWS*.
- Examples:
	- `aws-apigateway.LambdaRestApi` represents an API Gateway backed by a Lambda function
	- `aws-ecs-patterns.ApplicationLoadBalancerFargateService` which represents an architecture that includes a Fargate cluster with Application Load Balancer
	- ![[attachments/Pasted image 20230301182140.png]]

### Development using CDK
- Create an app
- Add code
- Build the app (optional)
- *Synthesise one or more stacks in the app to create an AWS CloudFormation template*. 
	- The synthesis step catches logical errors in defining your AWS resources.
- Deploy: `cdk deploy`

### CDK vs SAM
- *SAM*:
	- **Serverless focused**
	- Write your template declaratively In JSON or YAML
	- Great for quickly getting started with Lambda
	- **Leverages CloudFormation**
- *CDK*:
	- **All AWS services**
	- Write infra in a programming language JavaScript/TypeScript, Python, Java, and .NET
	- **Leverages CloudFormation**

