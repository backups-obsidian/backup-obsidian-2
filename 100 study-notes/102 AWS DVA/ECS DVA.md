---
created: 2022-05-15 11:30
updated: 2023-02-22 11:28
---
---
**Links**: [[102 AWS DVA Index]]
**Recommended Reads**: [[../101 AWS SAA/ECS | ECS]]

---
## Miscellaneous
- If you *terminate a container instance* while it is in the 
	- **STOPPED** state, that container instance **ISN'T automatically removed from the cluster**. 
		- You will need to remove your container instance in the STOPPED state by using the Amazon ECS console or AWS Command Line Interface. 
	- **RUNNING** state that container instance is **automatically removed**, or deregistered, from the cluster. 

> [!question]- Question related to *cluster name parameter*
> You are working for a shipping company that is automating the creation of ECS clusters with an Auto Scaling Group using an AWS CloudFormation template that accepts cluster name as its parameters. *Initially*, you launch the template with *input value 'MainCluster'*, which deployed five instances across two availability zones. The *second time, you launch the template with an input value 'SecondCluster'*. However, the instances created in the second run *were also launched in 'MainCluster'* even after specifying a different cluster name. What is the root cause of this issue?
>
> - The **cluster name Parameter has not been updated** in the file `/etc/ecs/ecs.config` during bootstrap
> - In the `ecs.config` file you have to configure the parameter `ECS_CLUSTER=your_cluster_name` to *register the container instance with a cluster named your_cluster_name*.

- Logging into ECR: `$(aws ecr get-login --no-include-email)` 
- Location of the config file `/etc/ecs/ecs.config`
	- We can configure the parameter `ECS_CLUSTER='your_cluster_name'` to register the container instance with a cluster named `'your_cluster_name'`

## ECS Tasks and Services 
- When we create an *ECS cluster* we specify the *launch types* (by default fargate is selected), VPC and subnets to be used.
- Task Definition indicates how to create an ECS task.
- We get 20 GBs of ephemeral storage from Fargate.
- If we are creating an *ECS Service we will need an ALB to balance the ECS tasks*.
	- We will need 2 SGs. One for ALB and other for ECS tasks.
- *ECS tasks can be invoked by EventBridge*. 
	- We can use them *over Lambdas* if the required execution time is more than 15 mins.
	- ![[attachments/Pasted image 20220515130441.png]]

> [!tip]- If you want to launch a *group of tasks then use a service*. If you want to run a single task then go with simple task configuration.
> In case of ECS tasks you won't get an option to use load balancing
> ---
> ![[attachments/Pasted image 20220515123458.png]]

> [!question]- You have a Classic ECS cluster that you want to *enable IAM roles* for your ECS tasks so that they can make API requests to AWS services. Which ECS configuration option should you enable in `/etc/ecs/ecs.config`?
> `ECS_ENABLE_TASK_IAM ROLE`

### Autoscaling
- We can manually increase or decrease the desired number of ECS tasks in a service. But this can also be done **automatically using AWS Application Auto Scaling**.
- We can scale on 3 metrics
	- ECS Service *Average CPU Utilisation*
	- ECS Service *Average Memory Utilisation* (RAM)
	- *ALB Request Count Per Target* metric coming from the ALB

- We can also use scaling methods like Target Tracking, Step Scaling, Scheduled Scaling.
- Fargate autoscaling is much easier to scale.

#### EC2 Launch Type - Auto Scaling EC2 Instances
- Accommodate ECS Service Scaling by adding underlying EC2 Instances
- Auto Scaling Group Scaling
	- Scale your ASG based on CPU Utilisation
	- Add EC2 instances over time
- ECS Cluster Capacity Provider (Recommended and easier)
	- Used to *automatically provision and scale the infrastructure* for your ECS Tasks
	- Capacity Provider *paired with an Auto Scaling Group*
	- Add EC2 Instances when you're missing capacity (CPU, RAM etc)

> [!tip] If you are using *EC2 Launch Type* then use *ECS Cluster Capacity Provider* to scale the EC2 instances.

## ECS Task Definitions
- Task definitions are metadata in **JSON** form to tell ECS *how to run a Docker container*
	- It is *like a docker compose file* for running one or more containers in a task.
- It contains *crucial formation, such as*: 
	- *Image Name*
	- *Port Binding for Container and Host* 
	- *Memory and CPU required* 
	- Environment variables 
	- Networking information 
	- *IAM Role* 
	- *Logging configuration* (ex CloudWatch)
- Can define *up to 10 containers in a Task Definition*.

> [!note]- **One** IAM role per Task Definition.
> - When you create an ECS service from a task definition with an IAM role then each ECS task in the service is automatically going to assume and inherit this ECS task role. 
> - But you should note that **IAM role is defined at the task definition level** and not at this service level. And so, therefore all the tasks  within your service, are going to get access to Amazon S3.
> ---
> ![[attachments/Pasted image 20220515150651.png]]

> [!question]- Where do you define an IAM role for an ECS task?
> On your task definition

> [!question]- You have a containerised application stored as Docker images in an ECR repository, that you want to run on an ECS cluster. You're trying to launch two copies of the **same Docker container** on the **same EC2 container instance**. The first container successfully starts, but the second container doesn't. You have checked that there's enough CPU and RAM on the EC2 container instance. What is the problem here?
> - The *host port defined in the task definition*.
> - *Random host port should be enabled in the task definition* when using EC2 launch type.
> - To enable *random host port*, set **host port = 0 (or empty)**, which allows multiple containers of the same type to launch on the same EC2 container instance.

### Environment variables
- Environment variables are defined in the task definition. They can from different places:
	- **Hardcoded**: When you have a *fixed* *non secret* value like URLs.
	- For **sensitive values** we can use either the SSM Parameter store or the Secrets Manager to store them and when an ECS task is launched these values will be fetched, resolved and injected at runtime as environment variables.
		- *SSM Parameter Store*: sensitive variables (e.g. API keys, shared configs)
		- *Secrets Manager*: sensitive variables (e.g., DB passwords)
	- Environment Files (**bulk**) *Amazon S3*
	- ![[attachments/Pasted image 20220515151345.png]]

### Data Volumes
- Data volumes bind mounts need to be mounted on the containers that need to share data.
- Using this we can **share data between multiple containers in the same Task Definition**.
- Works for both EC2 and Fargate tasks
- *EC2 Tasks* use EC2 *instance storage* for data volume bind mounts.
	- Data are tied to the lifecycle of the EC2 instance
- *Fargate* Tasks using *ephemeral storage*
	- Data are tied to the container(s) using them
	- 20 GiB 200 GiB (**default 20 GiB**)

*Use cases*:
- **Share ephemeral data between multiple containers**. Ephemeral is the key word here. 
- **Sidecar** container pattern, where the sidecar container is used to send metrics/logs to other destinations (separation of concerns)

## ECS Task Placement
> [!caution] Only for *EC2 launch type*.

- When a task of type EC2 is launched, *ECS must determine where to place it*, with the constraints of CPU, memory, and available port.
- Similarly, when a service scales in, *ECS needs to determine which task to terminate*. 
- To assist with this, you can define a **Task Placement Strategy** and **Task Placement Constraints**.

### Task Placement Process
- When Amazon ECS places tasks, it uses the following process to select container instances:
	- Identify the instances that *satisfy the CPU, memory, and port requirements* in the task definition.
	- Identify the instances that satisfy the *task placement constraints*.
	- Identify the instances that satisfy the *task placement strategies*.
	- Select the instances for task placement.

### Task Placement Strategies
- The strategies can be mixed together.
	- ![[attachments/Pasted image 20220515153151.png]]

#### Binpack
- Place tasks based on the **least available amount of CPU or memory**
- This *minimises the number of instances* in use (**cost savings**)

#### Random
- Places the task randomly.
- **Least configuration required**.
- It still makes sure that *tasks are scheduled on instances with enough resources to run them*.

#### Spread
- Place the task evenly based on the specified value
- Example: `instanceld`, `attribute:ecs.availability-zone` (this ensures HA)
- By **default**, ECS uses spread with the _ecs.availability-zone_ attribute to place tasks

### Task Placement Constraints
- `distinctInstance`: place **each task on a different container instance**
- `memberOf`: places task on instances that satisfy an **expression**
	- Uses the *Cluster Query Language* for grouping (advanced)

> [!question]- A developer is designing an application which will be hosted in ECS and uses an EC2 launch type. You need to *group your container instances by certain attributes such as Availability Zone, instance type, or custom metadata*. After you have defined a group of container instances, you will need to customise Amazon ECS to place tasks on container instances based on the group you specified. Which of the following ECS features provides you with **expressions that you can use to group container instances** by a specific attribute?
> CQL (DOUBTFUL about the answer I think it should be task placement constraints)
> ---
> **Task Placement Constraint** is incorrect because it is just a *rule that is considered during task placement*. Although it uses cluster queries when you are placing tasks on container instances based on a specific expression, *it does not provide the actual expressions which are used to group those container instances*.

> [!question]- A Developer is creating a service on Amazon ECS and needs to ensure that *each task is placed on a different container instance*. How can this be achieved?
> - Use task placement constraint of distinctInstance.
> - We cannot use task placement strategies since they are just task placement algorithms. Spread does not guarantee that each task is placed in a different EC2 instance.

> [!question]- A Developer is deploying an application using Docker containers on Amazon ECS. One of the containers runs a database and *should be placed on instances in the “databases” task group*. What should the Developer use to control the placement of the database task?
> Task placement constraint: `memberOf`
> ![[attachments/Pasted image 20230215094507.png]]