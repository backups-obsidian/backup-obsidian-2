---
created: 2022-05-01 18:34
updated: 2022-05-01 18:37
---
---
**Links**: [[101 AWS SAA Index]]

---
## SQS + Lambda
- DLQ is at the SQS side.
	- ![[attachments/Pasted image 20220501183401.png]]

## SNS + Lambda
- If we connect **SNS + Lambda** and *lambda fails* to process the message it will try it *internally 3 times*. 
- If we want to preserve the message then we can *setup a DLQ at the lambda service level*.
	- ![[attachments/Pasted image 20220501183206.png]]
	- This is a different architecture. For **SQS + Lambda** *DLQ is set at the SQS side* whereas for *SNS the DLQ is set at the lambda side*.

## S3 Events
- We know S3 events have **3 destinations** → **SQS**, **SNS**, **Lambda**
- Enable **versioning**
- If you are using lambda to consume the events then it is advised to have a *DLQ on the lambda's side*.
	- ![[attachments/Pasted image 20220501183712.png]]
