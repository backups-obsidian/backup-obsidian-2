---
created: 2022-05-16 12:39
updated: 2022-06-09 15:16
---
---
**Links**: [[102 AWS DVA Index]]

---
- We want to *deploy our application automatically* to many EC2 instances. These EC2 instances are *not managed by Elastic Beanstalk*.
- There are several ways to handle deployments using *open-source tools* (Ansible, Terraform, Chef, Puppet, etc)
- We can use the **managed service** AWS CodeDeploy.

## Working
- If we are deploying to *EC2 instances/On premises server* then the **CodeDeploy agent** must be running on the instances.
	- ![[attachments/Pasted image 20220517094620.png]]
- The agent is *continuously polling* AWS CodeDeploy for work to do 
- Application + `appspec.yml` (root of the code) is pulled from **GitHub or S3**.
- EC2 instances will run the *deployment instructions* in `appspec.yml`
- CodeDeploy Agent will report of success/failure of the deployment

## Components
- *Application*: a unique name functions as a container (revision, deployment configuration, ...)
- *Compute Platform*: **EC2/On-Premises**, **AWS Lambda**, or **Amazon ECS**
- *Deployment Configuration*: a set of deployment rules for success/failure
	- EC2/On-premises: specify the minimum number of healthy instances for the deployment
	- AWS Lambda or Amazon ECS: specify how traffic is routed to your updated versions
- *Application Revision*: application code + `appspec.yml` file
- *Target Revision*: the most recent revision that you want to deploy to a Deployment Group

### Deployment Groups:
- A **set of tagged EC2 instances** to which the code will be deployed.
- You can specify *one or more deployment groups* for a CodeDeploy application
- Directly to an ASG
- Mix of ASG / Tags so you can build deployment segments
- Customisation in scripts with `DEPLOYMENT_GROUP_NAME` environment variables

### Deployment Type
- Deployment Type is a method used to *deploy the application to a Deployment Group*. 
- It is of *2 types*

#### In-place Deployment
- Supports **EC2/On-Premises**
- Updates the instances in the deployment group with the latest application revisions. During a deployment, *each instance will be briefly taken offline for its update*.
	- ![[attachments/Pasted image 20220517100439.png]]

> [!tip] If we only have EC2 instances and no ASG then we cannot use Blue/Green deployment.

#### Blue/Green Deployment
- Supports **EC2 instances only**, **AWS Lambda**, and **Amazon ECS**
- Replaces the instances in the deployment group with *new instances* and deploys the latest application revision to them. After instances in the replacement environment are registered with load balancer, *instances from the original environment are deregistered and can be terminated*. 
	- ![[attachments/Pasted image 20220517100450.png]]
- Must have an *ASG* and *Load Balancer*.

### Permissions
- **IAM Instance Profile**: give *EC2 instances* the permissions to *access both S3/GitHub*
- **Service Role**: an *IAM Role for CodeDeploy* to perform operations on EC2 instances, ASGs, ELBs, etc.

## `appspec.yml`
- *files*: how to source and copy from S3/GitHub to filesystem. We have *source* and *destination*.
- *hooks*: set of *instructions* which correspond to lifecycle events such as (hooks *can have timeouts*) :
	- ApplicationStop
	- DownloadBundle
	- Beforelnstall
	- Install
	- Afterlnstall
	- ApplicationStart
	- **ValidateService**: Run at the end to make sure our service is properly deployed to our EC2 instance.

## Deployment Configuration
### Deployment Strategies
- *One At A Time*: one EC2 instance at a time, if one instance fails then deployment stops
- *Half At A Time*: 50%
- *All At Once*: quick but no healthy host, **downtime**. Good for **dev**.
- *Custom*: min healthy host = 75%

### Failures
- EC2 instances stay in "Failed" state
- New deployments will first be deployed to **failed instances**
- *To rollback, redeploy old deployment or enable automated rollback for failures*.

#### Rollbacks
- *Rollback*: redeploy a *previously deployed revision* of your application
- Deployments can be rolled back:
	- *Automatically* rollback when a deployment fails or rollback when a CloudWatch Alarm thresholds are met
	- *Manually*

> [!caution] If a roll back happens, CodeDeploy **redeploys the last known good revision** as a **new deployment** (not a restored version)
