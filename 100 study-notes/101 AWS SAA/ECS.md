---
created: 2022-04-25 20:00
updated: 2023-02-14 13:05
---
---
**Links**: [[101 AWS SAA Index]]
**Recommended Reads**: [[../102 AWS DVA/ECS DVA | ECS DVA]]

---
- It stands for **Elastic Container Service**
- It allows us to **launch docker containers on AWS**.
- ECS has *integration with ALB*.
- AWS takes care of starting and stopping the containers.
- ECS *tasks are abstraction over Containers*.
	- ![[attachments/Pasted image 20220425195624.png]]

- ECS *services* and *cluster*
	- `Container → Task → Service → Cluster`
	- Suppose we have an *ECS cluster with EC2 launch type*.
	- We **can have multiple containers in a task**. Like sidecar containers. 
		- A task is similar to a pod in k8s.
	- In a **service we can have multiple tasks** spread across EC2 instances.
	-  If we wanted to **expose these tasks** then we could create an **ALB**.
	- We can have **multiple services** in an *ECS cluster*.
 

- You can use **CloudWatch Events** to *run Amazon ECS tasks* when certain AWS events occur. 

## Launch Types
- ECS has *2 launch types*.
- Cost:
	- ECS with *EC2 launch type* is charged based on **EC2 instances** and **EBS volumes used**. 
	- ECS with *Fargate launch type* is charged based on **CPU** and **memory resources** that the containerised application requests.

### EC2 Launch Type
- To have an *ECS cluster* across *multiple AZs* we create an *ASG* and place some EC2 instances in it.
	- ![[attachments/Pasted image 20220425195833.png]]
- The containers will belong to this ASG and we want to register these machines with ECS. To do so we run the **ECS agent on these EC2 instances**. We don’t have to do this manually if we are using an **AMI made for ECS**. The **ECS agent** will come **preconfigured**.
- With the help of the **ECS agent** all the **machines** will be **registered** to the **ECS cluster**.
- To launch ECS tasks we have to create EC2 instances.

### Fargate Launch Type
- In Fargate here is **NO need to provision the infrastructure**
- It is a *lot simpler* and is known as the **serverless** offering.
- AWS will *run the containers based* on the **CPU or RAM** we need.
- Tasks are automatically created without the need to start EC2 instances. To access the **Fargate task** we will have **ENIs (Elastic Network Interface)** which will also be launched within our VPC. They will **bind** to the fargate task.
- **Each task** will have a **unique ENI**.
- Since *each task will get its own unique private IP* we need to make sure that our VPC has these many private IPs.
	- ![[attachments/Pasted image 20220425200054.png]]

## IAM Roles for ECS
- These tasks will be interacting with different services we need IAM roles for them.
- Each task should have a *specific role* known as **task role**.
- You define the **Task Role** to use 
	- in your *task definitions* 
	- you can use a `taskRoleArn` *override* when running a task *manually* with the RunTask API operation. 
- With the *task roles* *only the task can access the particular services*.

> [!important]+ Different *roles for different launch types*
> - Note that there are **instances roles** (also known as *EC2 Instance profile*) and **task roles** that you can assign in ECS when using the *EC2 launch type*. 
> - You *should not apply the permissions to the container instance* (instance role) as they will then *apply to all tasks running on the instance* as well as the instance itself. 
> - In case of *fargate* you can *only assign task roles*.
> ---
> ![[attachments/Pasted image 20220425200744.png]]

> [!note]+ Difference between *task role* and *instance role (EC2 instance profile)*
> *EC2 Instance Profile*: All the *administration and control tasks*.
> - **Used by the ECS agent**
> - Makes API calls to ECS service
> - Send *container logs to Cloud Watch Logs*
> - *Pull Docker image* from ECR
> - *Reference sensitive data* in Secrets Manager or SSM Parameter Store
> 
> *ECS Task Role*: Permissions related to *talking to services*.
> - Each task should have a *specific role*.
> - Use *different roles for the different ECS Services* you run
> - Task Role is defined in the task definition. 
> ---
> ![[attachments/Pasted image 20220425201355.png]]


> [!question]- A company is developing a new online game that will run on top of Amazon ECS. *Four distinct Amazon ECS services* will be part of the architecture, *each requiring specific permissions* to various AWS services. The company wants to optimise the use of the underlying Amazon EC2 instances by *bin packing* the containers based on memory reservation. Which configuration would allow the Development team to meet these requirements *MOST securely*?
> - Create *four distinct IAM roles*, each containing the required permissions for the associated ECS services, then *configure each ECS task definition* to reference the associated IAM role.
> - It is a **best practice** to **use IAM roles for tasks** instead of assigning the roles to the *container instances*.

## ECS Data Volumes
- ECS has an integration with **EFS file system**. We can easily share data between ECS tasks *between AZs*.
- Tasks launched in any AZ will be able to share the same data in the EFS volume
- **Fargate + EFS** = **serverless** + data storage without managing servers
- We can *mount the EFS file system* on these ECS tasks.
- Works for **both EC2 instances and fargate tasks**.
	- ![[attachments/Pasted image 20220425201822.png]]

> [!caution] FSx For *Lustre* is **not supported**. Amazon **S3** *cannot be mounted as a file system*.

## Load Balancing
### EC2 Launch Type
- **ALB** supports **dynamic port mapping**. 
- It *will find the right port on the EC2 instance*.
- You must allow on the EC2 instance's security group *any port* from the ALB security group
	- ![[attachments/Pasted image 20220425202947.png]]

### Fargate Launch Type
- Each **task has a unique private IP**.
- You must allow on the *ENI's security group the task port from the ALB security group*.
	- ![[attachments/Pasted image 20220425203111.png]]

## Scaling 
- ECS service **scales automatically** to launch a new task. 
- This works for both fargate and EC2 launch type. The only difference is that *for EC2 launch type you will have to sometimes scale the EC2 instances with an ASG* to create space for new tasks.
- The following metrics are available for ECS Service: 
	- `ECSServiceAverageCPUUtilization`
	- `ECSServiceAverageMemoryUtilization`
	- `ALBRequestCountPerTarget`

## Rolling Updates
- When updating ECS from v1 to v2 we can control how many tasks will be stopped, started and in which order.
	- ![[attachments/Pasted image 20220425204027.png]]