---
created: 2022-05-16 10:21
updated: 2023-02-17 12:27
---
---
**Links**: [[102 AWS DVA Index]]
**Recommended Reads**: [[../101 AWS SAA/ElasticBeanstalk | ElasticBeanstalk]]

---
- BeanStalk supports almost all programming languages. If your language is incompatible with Beanstalk & doesn't use Docker then you *can create your own custom platform*.
	- You will need to create your own platform using **Packer** (open source tool to create AMIs). 

> [!tip] If the question mentions **Packer** then go for custom platform.

- We have a CLI specific to BeanStalk known as **EB CLI**. It can be helpful to *automate the deployments*.
	- It helps us increase our efficiency when compared to the normal AWS CLI.
	 - We can package the application as a *zip file* and deploy it using the *`eb deploy`* command.
- You can run **cron jobs** in EB by defining a **`cron.yaml`** file and using the **[[../101 AWS SAA/ElasticBeanstalk#Web vs Worker Tier|Worker Environment]]**
	- It should be at the **root of the repository**. NOT inside `.ebextensions`.

> [!caution] All the *source bundles* (code *zip files* for deployment) are stored in **S3**.

> [!note]- We cannot configure Amazon Athena, **AWS Lambda**, and **Amazon CloudFront** on ElasticBeanstalk.
> We can configure *EC2*, *Load Balancers* and *CloudWatch*.

- When you use the AWS Elastic Beanstalk console to *deploy a new application* or an application version, you'll need to **upload a source bundle**. Your source bundle must meet the following requirements:
	  - Consist of a **single ZIP file** or WAR file (you can include multiple WAR files inside your ZIP file)
	  - **NOT exceed 512 MB**
	  - **NOT include a parent folder** or top-level directory (subdirectories are fine)

## Components
- **Application**: An Elastic Beanstalk _application_ is a logical collection of Elastic Beanstalk components, including _environments_, _versions_, and _environment configurations_. In Elastic Beanstalk an application is conceptually similar to a folder.
- **Application Version**: an iteration of your application code 
	- ![[attachments/Pasted image 20220516102434.png]]
- **Environment**:
	- *Collection of AWS resources* running an application version (**only one application version at a time**)
	- *Tiers*: Web Server Environment Tier & Worker Environment Tier
- You **can create multiple environments** (dev, test, prod, etc) **in one application version**. 
- If you want multiple application versions running at the same time then you have to create multiple applications.

> [!note]+ In summary, an *application is a collection of components*, a *version is a specific release of code*, and an *environment is a deployed version of the code on a **set of resources**.
> You can have multiple environments running at the same time in a single application in Amazon Elastic Beanstalk. 

## Lifecycle policy
- Elastic Beanstalk can store at most **1000** application versions
- If you don't remove old versions, you won't be able to deploy anymore
- To *phase out old application versions*, use a **lifecycle policy**
	- Based on **time** (*old versions are removed*)
	- Based on **number of versions** (when you have *too many versions*)
	- ![[attachments/Pasted image 20220516124218.png]]

- *Versions that are currently used won't be deleted*.
- *Option not to delete* the *source bundle in S3* to prevent data loss. If the source bundle is present we can very easily restore a particular version.

## Extensions
- A zip file containing our code is deployed to Elastic Beanstalk for new environments.
- All the *parameters set in the UI can be configured with code using files*.
- Requirements:
	- In the `.ebextensions/` directory in the *root of source code*
	- *YAML / JSON* format
	- **Even though they are in YAML/JSON the file must with `.config`** extension (example: logging.config)
- We can *modify some default settings* using: `option_settings`
- **Ability to add any resource** such as RDS, *ElastiCache*, DynamoDB, etc *which cannot be added using the UI*.

> [!note] Although it is not advised to create RDS using EB, you can create ElastiCache clusters using EB.

> [!important] We can use `.ebextensions` to provision any resource since ElasticBeanstalk **uses CloudFormation under the hood**.

- Resources managed by `.ebextensions` get deleted if the environment goes away

## Cloning
- We can clone an environment with the exact same configuration
- Useful for **deploying a test version** of your application
- **All resources and configuration are preserved**:
	- **Load Balancer type** and configuration. So it cannot be used for changing the load balancer type.
	- *RDS database type* (but the *data is not preserved*)
	- *Environment variables*
- After cloning an environment, you *can change settings*.

## Using environment variables
- In Elastic Beanstalk, you can include a **YAML formatted environment** manifest in the **root of your application source bundle** to configure the environment name, solution stack and environment links to use when creating your environment.
	- `env.yaml` 

## Migrations 
### Changing Load Balancer
- After creating an Elastic Beanstalk environment, you **cannot change the Elastic Load Balancer type** (only the configuration)
- If we want to *change the Load Balancer type* then we have to *perform migration*.
	- ![[attachments/Pasted image 20220516150128.png]]
- To migrate:
	- Create a new environment with the same configuration *except LB* (**can't clone**)
	- Deploy your application onto the new environment
	- Perform a **CNAME swap** or Route 53 update

### Decoupling RDS
- RDS can be provisioned with Beanstalk, which is *great for dev/test*
- This is **not great for prod** as the *database lifecycle is tied to the Beanstalk environment lifecycle*
- The best for prod is to **separately create an RDS database** and refer it using environment variables.
- **Decoupling RDS**:
	- *Create a snapshot* of RDS DB (as a safeguard)
	- Go to the RDS console and **protect the RDS database from deletion**
	- Create a *new Elastic Beanstalk environment, without RDS*, point your application (in the new EB) to existing RDS
	- Perform a *CAME swap* (blue/green) or Route 53 update, confirm working
	- Before terminating the old Elastic Beanstalk environment, *remove its security group rule first before proceeding*. If the existing security group rule is not removed then it hinders the deletion of the old environment.
	- *Terminate the old environment* (**RDS won't be deleted** since we have a delete protection) 
	- The CloudFormation stack won't be deleted since the RDS was protected from delete. 
	- *Delete CloudFormation stack* (in DELETE_FAILED state)

### One AWS account to another
- The project manager of a team wants to *migrate the Elastic Beanstalk environment from Team A's AWS account into Team B's AWS account*.
	- Create a *saved configuration* in Team A's account and download it to your local machine.
	- Make the account-specific parameter changes and upload to the S3 bucket in Team B's account. 
	- From Elastic Beanstalk console, create an application from *Saved Configurations*
- You must use **saved configurations to migrate an Elastic Beanstalk environment between AWS accounts**.
	- There is no direct Export and Import option for migrating Elastic Beanstalk configurations.

## Using HTTPS
### Beanstalk with HTTPS
- Idea: **Load the SSL certificate onto the Load Balancer**
- Can be done from the Console (EB console, load balancer configuration)
- Can be done from the code: **`.ebextensions/securelistener-alb.config`**
- SSL Certificate *can be provisioned using ACM* (AWS Certificate Manager) or CLI
- Must *configure a security group rule to allow incoming port 443* (HTTPS port)

### Redirect HTTP to HTTPS
- Configure your instances to redirect HTTP to HTTPS:
- *OR configure the Application Load Balancer (ALB only) with a rule* 
- *Make sure health checks are not redirected* (so they keep giving 200 OK)