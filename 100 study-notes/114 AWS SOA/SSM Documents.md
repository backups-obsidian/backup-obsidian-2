---
created: 2023-03-02 14:39
updated: 2023-03-10 08:34
---
---
**Links**: [[114 AWS SOA Index]]

---
## SSM Documents
- Documents can be in **JSON or YAML**.
- We define *parameters and actions*
	- ![[attachments/Pasted image 20230302144607.png]]
	- We can customise the parameter while running the command.
- Many documents already exist in AWS

### Run command
- **Execute a document** (= script) or *just run a command*.
- *Run command across multiple instances* (using [[Systems Manager (SSM)#Resource Groups | resource groups]])
- *Rate Control*/Error Control
	- Number of targets on which you want to run commands.
- Integrated with IAM & Cloud Trail
- **No need for SSH**
	- Everything happens through the SSM agent.
- Command *Output* can be shown in the Console, sent to *S3 bucket* or *CloudWatch Logs*.
- Send *notifications to SNS* about command statues (In progress, Success, Failed, ...)
- Can be *invoked using EventBridge*.