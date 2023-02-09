---
created: 2022-05-16 16:54
updated: 2023-02-09 14:13
---
---
**Links**: [[102 AWS DVA Index]]

---
- **Alternative to Jenkins**.
- **Source**: 
	- *CodeCommit* 
	- *S3* 
	- Bitbucket 
	- *GitHub*
- **Build instructions**: Code file `buildspec.yml` (at the **root** of the source code) or insert *manually in CodeBuild console*.
- *Output logs* can be stored in Amazon *S3* & *CloudWatch Logs*
- Use CloudWatch Metrics to monitor build statistics
- Use *CloudWatch Events to detect failed builds* and trigger notifications
- Use *CloudWatch Alarms to notify if you need thresholds* for failures
- **Build Projects can be defined within CodePipeline or CodeBuild**
- CodeBuild **uses containers** to run the environments.
- You can use a list of supported environments for building your code and if your environment is not there then you *can use docker*.
- **CodeBuild can be run locally by using docker and CodeBuild Agent**. 
	- This is good for *troubleshooting beyond logs*.
- We have **Codebuild timeouts** to terminate builds queued for a long time.
- **Uses**: 
	- *Build Code*
	- Run some *automated tests* before deployment.

> [!important]+ By default CodeBuild containers are launched **outside VPC**.
> - We can specify a *VPC configuration*: **VPC ID**, **Subnet IDs**, **Security Group IDs**
> - Then your build can access resources in your VPC (e.g., RDS, ElastiCache, EC2, ALB, etc)
> - *Use cases*: integration tests, *data query*, *internal load balancers*, etc.

> [!question]- You CodeBuild project fails what can you do to debug?
> - Review the *logs in S3 or CloudWatch*.
> - *Run CodeBuild locally* to reproduce the build
> ---
> You **cannot SSH into the CodeBuild containers**. CodeBuild containers are *deleted at the end of their execution* (success or failure). You can't SSH into them, even while they're running.

## How it works
![[attachments/Pasted image 20220516185423.png]]

## `buildspec.yml`
- Must be at the **root of the source code**.
- We can define *env variables* either in *plain text* or we can use either *SSM Parameter Store* or *Secrets Manager*.
- **phases**: specify **commands** to run. *install*, *pre_build*, *Build*, *post_build*.
- **artifacts**: what to *upload to S3* 
- *Encryption of artifacts with KMS*:
	- For AWS CodeBuild to encrypt its build output artifacts, it needs access to an AWS KMS customer master key (CMK). 
	- By default, AWS CodeBuild uses the AWS-managed CMK for Amazon S3 in your AWS account. 
	- To use encryption the following *environment variable* should be used: `CODEBUILD_KMS_KEY_ID`. 
- **cache**: files to cache (usually *dependencies*) to **S3** for future **build speedup**

> [!tip] Enabling *dependency caching* using S3 can drastically speed up your build.

> [!important] CodeBuild can **run any commands** specified in `buildspec.yml`, so you can use it to run commands including *build a static website and copy your static web files to an S3 bucket*.