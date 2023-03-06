---
created: 2022-04-19 16:22
updated: 2023-03-06 09:57
---
---
**Links**: [[101 AWS SAA Index]]

---
## Introduction
- It is a way to get **governance**, **compliance** and **audit** for your *AWS Account*.
- CloudTrail is **enabled by default**.
- Get an **history** of **all** the **events / API calls** made within your AWS Account by: **Console**, **SDK**, **CLI**, **AWS Services**

- A trail can be **applied** to a **single region** or **all the regions (default)** when a trial is created.
	- For non global services, *events are recorded in the region where the action occurred*. For **global services** such as AWS Identity and Access Management (IAM), AWS STS, Amazon CloudFront, and Route 53, events are delivered to any trail that includes global services, and are **logged as occurring in US East (N. Virginia) Region**.
	- CloudTrail can be used with *multi-region trail enabled*, however, it will only cover the activities of the regional services (EC2, S3, RDS etc.) and *not for global services* such as IAM, CloudFront, AWS WAF, and Route 53. In order to satisfy the requirement, you have to add the `--include-global-service-events` parameter in your AWS CLI command.

- If a *resource* was *deleted* in AWS then *investigate CloudTrail* first to find out who did it.
- We can create new trails but we have a default trail.

> [!note] To find out **who did it**.

### Event Storage
- Events created by CloudTrail are can be sent to **S3** or **CloudWatch logs**.
- Events by **default** are stored for **90 days** *in CloudTrail*. They are *deleted after that*.
- To *keep events longer* than the default period we need to **log them to S3** and use *Athena* to analyse them. 
	- For this we have to *create a new trail*. **The default trail will not log them in S3**. 
	- ![[attachments/Pasted image 20220428083443.png]]

- We can also *send the logs to CloudWatch logs group* for longer retention.

> [!question]- Why use S3 for log storage?
> - By default, Cloud Trail event *log files are encrypted using Amazon S3 server-side encryption* **(SSE)**. 
> - You *can also choose to encrypt* your log files with an AWS Key Management Service (*AWS KMS*) key. 
> - You can store your log files in your bucket for *as long as you want*. You can also define Amazon S3 *lifecycle rules to archive or delete* log files automatically. 

## CloudTrail Events
### Management Events
- Operations that are **performed on resources** in your AWS account
- By **default**, trails are **configured to log management events**.
- We can **separate** **Read Events** (that don't modify resources) from **Write Events** (that may modify resources). Since *Write Events are much more destructive in nature*.
- *Some examples* of management events are: Configuring security (IAM AttachRolePolicy), Configuring rules for routing data (Amazon EC2 CreateSubnet), Setting up logging (AWS CloudTrail CreateTrail)

### Data Events
- By **default** **these events are NOT logged** (because of **high volume** operations)
- Examples of high volume events are:
    - Amazon **S3 object-level activity** (ex: `GetObject`, `DeleteObject`, `PutObject`)
    - AWS **Lambda function** execution activity (the **Invoke API**)
- In this also we can **separate read from write events**.

### Insight Events - CloudTrail Insights
- When we have so many management events across all types of services and so many APIs getting called very very quickly in our account, it can be **difficult to identify what looks odd**.
- We can **enable CloudTrail Insights to find out *unusual activity*** in our account.
	- Inaccurate resource provisioning
	- *Hitting service limits*
	- *Bursts of AWS IAM actions*
	- Gaps in periodic maintenance activity
- We have to *pay for this service*.
- Cloud Trail Insights *analyses normal* **management events** to create a **baseline** and then continuously *analyses **write events** to detect unusual patterns*.
	- ![[attachments/Pasted image 20220428082455.png]]
	- **Only management events** are analysed.
- *Insights also generates events* like management and data events.

## Organisational Trails
- You can create a CloudTrail trail in the *management account with the organisation trails option enabled* and this **will create the trail in all AWS accounts** within the organisation.
- Trail with the same name will be created in every AWS account
- Member accounts _**can see the organisation trail** but can't modify or delete it_. 
- By **default, member accounts don't have access to the log files** for the organisation trail in the Amazon S3 bucket.

## Miscellaneous
### Log File Integrity Validation (SOA)
- **Digest Files**:
	- References the log files for the last hour and contains a *hash of each*
	- Stored in the same S3 bucket as log files (different folder)
- Helps you *determine whether a log file was modified/deleted after CloudTrail delivered it*.
	- SHA-256 hashing

### Integration with EventBridge (SOA)
- Used to *react to any API call being made in your account*.
- CloudTrail is **NOT real-time**.
	- Delivers an event within 15 minutes of an API call.
	- Delivers log files to an S3 bucket every 5 minutes.