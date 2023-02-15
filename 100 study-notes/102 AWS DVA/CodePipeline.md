---
created: 2022-05-16 12:39
updated: 2023-02-15 08:43
---
---
**Links**: [[102 AWS DVA Index]]
**Keywords**: *Manual approval*, *orchestration*

---
- *Visual Workflow* to **orchestrate your CICD**
- *Sources*:
	- **CodeCommit** 
	- *ECR* 
	- **S3**
	- *GitHub*

- *Build*: *CodeBuild*, Jenkins, CloudBees, TeamCity
- *Test*: *CodeBuild*, AWS Device Farm, 3rd party tools
- *Deploy*: *CodeDeploy*, Elastic Beanstalk, CloudFormation, ECS, S3,

- **Consists of stages**:
	- Each stage can have **multiple sequential actions and/or parallel actions**
	- Example stages: Build (CodeBuild)  → Test (CodeBuild) → Deploy (CodeDeploy) → Load → Testing
- **Manual approval action** can be defined at any stage.
- If any of the stages fail before deploying the code then it won't deploy the code. Like if some tests fail in CodeBuild.

> [!tip]- Stages can have **multiple action groups**.
> ![[attachments/Pasted image 20220516165011.png]]
> ---
> Here + Add Action on the right hand side is for parallel actions.

## Artifacts
- **Each pipeline stage can create artifacts**
- Artifacts stored in an **S3 bucket** and *passed on to the next stage*

![[attachments/Pasted image 20220516164209.png]]

## Troubleshooting
- For CodePipeline Pipeline/Action/Stage *Execution State Changes* we can use **CloudWatch Events** (Amazon EventBridge). Example:
	- You can create events for *failed pipelines*
	- You can create events for *cancelled stages*

> [!question]- A company has implemented AWS CodePipeline to automate its release pipelines. The Development team is writing an *AWS Lambda function that will send notifications for state changes* of each of the actions in the stages.
> Create an Amazon **CloudWatch Events rule** that uses CodePipeline as an event source.

- If CodePipeline fails a stage, your pipeline stops, and you can *get information in the console*
- If pipeline *can't perform an action*, make sure the *IAM Service Role* attached does have *enough lAM permissions* (IAM Policy)
- *AWS CloudTrail* can be used to *audit AWS API calls*

> [!caution] You **can't use SNS** for getting notifications on failures.