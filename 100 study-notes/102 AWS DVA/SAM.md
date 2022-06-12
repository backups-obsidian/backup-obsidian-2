---
created: 2022-05-16 12:39
updated: 2022-05-27 13:37
---
---
**Links**: [[102 AWS DVA Index]]

---
## SAM
- It stands for **Serverless Application Model**
- **Framework** for developing and deploying serverless applications
- All the configuration is *YAML code*
- **Generate complex CloudFormation from simple SAM YAML file**
- *Supports anything from CloudFormation*: Outputs, Mappings, Parameters, Resources...
- *Only two commands to deploy to AWS*
- SAM can use *CodeDeploy to deploy Lambda functions*
- SAM can help you to run Lambda, API Gateway, DynamoDB **locally**
- SAM Deployment Process
	- ![[attachments/Pasted image 20220527111606.png]]

> [!note] The template you write in SAM is converted to a CloudFormation template. Any time you see the keyword **Transform** in a template then it means it is a *SAM template*

### Recipe
- **Transform Header** indicates it's SAM template:
	- Transform: `AWS::Serverless-2016-10-31`
- Write Code
	- `AWS::Serverless::Function`
	- `AWS::Serverless::Api`
	- `AWS::Serverless::SimpleTable`
- **Package & Deploy**:
	- `aws cloudformation package` / `sam package`
	- `aws cloudformation deploy` / `sam deploy`

### CLI Debugging
- *Locally build, test, and debug your serverless applications* that are defined using AWS SAM templates
- Provides a *lambda-like execution environment* locally
- *SAM CLI + AWS Toolkits* => step-through and debug your code
- **AWS Toolkits**: *IDE plugins* which allows you to build, test, debug, deploy, and invoke Lambda functions built using AWS SAM

### SAM Policy Templates
- List of templates to **apply permissions to your Lambda Functions**
	- ![[attachments/Pasted image 20220527121444.png]]
- Important examples:
	- `S3ReadPolicy`: Gives read only permissions to objects in S3
	- `SQSPollerPolicy`: Allows to poll an SQS queue
	- `DynamoDBCrudPolicy`: CRUD 
- The idea is to focus on what you want to do rather than focusing on how to do it (like worrying about policies)

### SAM & CodeDeploy
- SAM framework natively uses *CodeDeploy to update Lambda functions*
- *Pre and Post traffic hooks* features to validate deployment (before the traffic shift starts and after it ends)
- *Easy & automated rollback* using CloudWatch Alarms

### SAR (Serverless Application Repository)
- **Managed repository** for serverless applications
	- ![[attachments/Pasted image 20220527131012.png]]
- The applications are *packaged using SAM*
- Build and publish applications that can be **re-used** by organisations
	- Can **share** publicly
	- Can **share with specific AWS accounts**
- This prevents duplicate work, and just go straight to publishing
- Application settings and behaviour can be customised using Environment variables

## SAM Exam Summary
- SAM is **built on CloudFormation**
- SAM requires the **Transform** and **Resources** sections
- Commands to know:
	- `sam build`: fetch dependencies and create local deployment artifacts
	- `sam package`: package and *upload to Amazon S3*, generate CF template
	- `sam deploy`: deploy to CloudFormation
- SAM *Policy templates for easy lAM policy definition*
- SAM is *integrated with CodeDeploy to do deploy to Lambda aliases*