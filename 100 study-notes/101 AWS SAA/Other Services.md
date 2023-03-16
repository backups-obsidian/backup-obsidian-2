---
created: 2022-04-19 16:22
updated: 2023-03-16 08:40
---
---
**Links**: [[101 AWS SAA Index]]

---
## Trusted Advisor
- It is an online resource to help you 
	- **reduce cost** 
	- **increase performance**
	- **improve security** 

- Trusted Advisor provides **real time guidance** to help you *provision your resources following AWS best practices*.
- AWS Trusted Advisor **offers a Service Limits check** (in the Performance category) that displays your usage and limits for some aspects of some services.
- No need to install anything **high level AWS account assessment**
- Analyses your AWS accounts and **provides recommendation**:
    - It analyses **5 categories** of problems → **Cost**, **performance**, **security**, **fault tolerance**, **service limits**.

- Trusted advisor has *2 tiers*:
    - **First Tier** → Core Checks and recommendations *for all customers*
    - **Full Trusted Advisor** → Available for **Business** & **Enterprise** **support plans**
        - *Programmatic Access* using AWS **Support API**

> [!note] Keywords: *service limits*, *questions*, *support API*, *real time*, **best practice**.

## CI/CD
- **CodeCommit** is the AWS version of *GitHub/GitLab*.
- **CodeBuild** is the AWS version of CI/CD tool like *Jenkins*.
- **CodeDeploy** is for deploying code. Like *blue/green strategy*.
- **CodePipeline** is for orchestrating everything.
	- CI/CD pipeline to deliver all the way to *ElasticBeanstalk*.
	- ![[attachments/Pasted image 20220430140016.png]]

## CloudFormation
- **CloudFormation** is a **declarative** way for of outlining our **AWS infrastructure** for any resources.
- CloudFormation works using **templates**.
- The **templates are uploaded in S3** and then referenced in CloudFormation.

> [!caution] To **update a template**, we **can't edit previous ones**. We have to **re-upload a new version** of the template to AWS

- **Template** is also known as **stack**. *Stacks are identified by a name*
- **Deleting a stack deletes every single artifact that was created by CloudFormation.**
- CloudFormation is IaC (infrastructure as code), 
- We can leverage other templates/stacks created by people and we don't have to reinvent the wheel. 
- We can have different stacks for different things for separation of concerns.
- We can use AWS CLI to fully automate infrastructure deployment using CloudFormation.
- Some important template components are *resources*, *parameters* and *outputs*.
- With **StackSets** we can Create, update, or delete stacks across **multiple accounts and regions** with a single operation. Useful in **AWS organisations**.
- Use **direct updates** when you want to quickly *deploy your updates*. 
- With **change sets**, you can *preview the changes AWS CloudFormation* will make to your stack, and then decide whether to apply those changes.

## Step Functions
- Build **serverless visual workflow** to **orchestrate** your **Lambda functions**
- Represent flow as a **JSON state machine**

> [!important] Key words in questions to go for Step Functions *serverless*, **state machine**, *workflow* or *serverless workflows*.

## SWF
- It stands for *simple workflow service*
- Code **runs on EC2** (**not serverless**)
- Amazon SWF provides useful guarantees around task assignments. **It ensures that a task is never duplicated** and is *assigned only once*.
- **Step Functions is recommended to be used for new applications**, except:
    -   If you need *external signals to intervene* in the processes
    -   If you need *child processes that return values to parent processes*

## EMR
- *Elastic Map Reduce*.
- EMR helps creating **Hadoop clusters (Big Data)** to **analyse and process vast amount of data**
- Also supports *Apache Spark*, HBase, Presto, Flink...
- EMR **takes care of all the provisioning and configuration**
- *Auto-scaling* and integrated with Spot instances
- **Use cases**: data processing, machine learning, web indexing, big data...

> [!note] Key words are **hadoop**,**managed**,**spark** and **big data**.

## Opsworks
- AWS Opsworks = **Managed Chef & Puppet**
- They work great with EC2 & *On Premise VM*.

> [!note] In the exam: **Chef & Puppet needed ⇒ AWS Opsworks**
- Chef (**Recipes**) and Puppet (**Manifests**) help in managing **configuration as code**.
- Chef / Puppet have similarities with SSM / Beanstalk / CloudFormation but they're **open-source tools that work cross-cloud**
- AWS only supports Chef and Puppet and not other configuration tools.

## Workspaces
- **Managed**, **Secure Cloud Desktop**
- Great to **eliminate management of on-premise VDI** (*Virtual Desktop Infrastructure*)
- **Integrated with Microsoft Active Directory**

## AppSync
- **Managed GraphQL**
- **Store and sync data across mobile and web apps in real-time**
-  Real-time subscriptions
-  Offline data synchronisation (replaces Cognito Sync)

> [!note] Keywords are *graphql*,*sync* and *mobile*.

## Cost Explorer
- **Visualise, understand, and manage your AWS costs** and usage over time
- Create *custom reports* that analyse cost and usage data.
- Choose an optimal **Savings Plan** (to lower prices on your bill)
- **Forecast** usage up to 12 months based on previous usage
- **Compute optimiser** is used to get *EC2 instance type recommendations*. 
	- It *doesn't give instance purchase recommendations* like going for reserved instances.

> [!note] Keywords: **recommendations**, **EC2 instances**, *optimise costs*, *reports*,*forecast*.


## Rekognition
- Rekognition is simply a service that can *identify the objects, people, text, scenes, and activities*, as well as detect any inappropriate content on your **images or videos**.

## ACM
- Managing **3rd party** certificates.
- AWS **Private Certificate manager**.
- If you got your certificate from a third-party CA, *import the certificate into ACM* or upload it to the *IAM certificate store*.

## X-RAY
- AWS X-Ray helps developers **analyse and debug production, distributed applications**, such as those built using a microservices architecture.
- You can use X-Ray to *collect and trace* data **across AWS Accounts**.
- Use case: There is a requirement to trace and *analyse user requests as they travel through your Amazon API Gateway APIs* to the underlying services.

## Systems Manager 
- **AWS Systems Manager Run Command** lets you *remotely and securely manage the configuration of your managed instances*.
- It can be *run from the AWS Management Console* that to **execute the script on all target EC2 instances**.

> [!note] Keywords: *run*, *script*, *EC2 instances*.

## Batch
- An AWS Batch **multi-node parallel job** is compatible with any framework that supports IP-based, *internode communication*, such as Apache MXNet, TensorFlow, Caffe2, or *Message Passing Interface* (MPI).
- AWS **Batch manages all the infrastructure for you**, avoiding the complexities of provisioning, managing, monitoring, and scaling your batch computing jobs.

> [!note] Keywords: *managed*, *minimise operational overhead*, *many EC2 instances*.

