---
created: 2023-03-03 09:25
updated: 2023-03-13 08:47
---
---
**Links**: [[114 AWS SOA Index]]

---
## User Data in EC2 for CloudFormation
- We can include user data script used in EC2 instances in CloudFormation.
- The important thing to pass is the **entire script through the function `Fn::Base64`**.

### `cfn-init`
- `AWS::CloudFormation::Init` must be in the **Metadata of a resource**
- With the `cfn-init` script, it *helps make complex EC2 configurations readable*
- The *EC2 instance will query the CloudFormation service* to get init data
	- ![[attachments/Pasted image 20230303094048.png]]
- *Logs* go to `/var/log/cfn-init.log`

> [!note] Just remember `cfn-init` is a *more readable way* to manage EC2 user data in CloudFormation.

> [!question]- You are provisioning an internal full LAMP stack using CloudFormation, and the EC2 instance gets configured automatically using the `cfn` helper scripts, such as `cfn-init` and `cfn-signal`. The stack creation fails as CloudFormation *fails to receive a signal from your EC2 instance*. What are the possible reasons for this?
> - The subnet where the application is deployed *does not have a network route to the CloudFormation service through a NAT Gateway or Internet Gateway*.
> - **The `cfn-signal` script does not get executed before the timeout of the wait condition**
> ---
> *The `cfn-init` script failed* is wrong since - The `cfn-init` script failure should still be followed by the `cfn-signal` script, *which would have sent a signal to CloudFormation nonetheless*. The question mentions that CloudFormation fails to receive a signal and not that the stack failed.

### `cfn-signal` and wait conditions
- When using `cfn-init` we still don't know how to tell CloudFormation that the EC2 instance got properly configured after a `cfn-init`.
	- For this, we can use the `cfn-signal` script.
- We **run `cfn-signal` right after `cfn-init`** to tell CloudFormation service to keep on going or fail.
- We need to define **WaitCondition**:
	- **Block the template until it receives a signal** from `cfn-signal`
		- ![[attachments/Pasted image 20230303094734.png]]
	- We attach a **CreationPolicy** (also works on EC2, ASG)
- If no input is received after the wait period specified in the WaitCondition then the stack creation fails.
- If you *forget to add the wait condition* then you would get a *CloudFormation success even though the resource is still in creation*.

#### Troubleshooting
- Wait Condition **Didn't Receive the Required Number of Signals** from an Amazon EC2 Instance.
- Ensure that the **AMl** you're using has the AWS **CloudFormation helper scripts installed**. 
	- If the AMI doesn't include the helper scripts, you can also download them to your instance.
- Verify that the `cfn-init` & `cfn-signal` command was **successfully run on the instance**.
	- You can view logs, such as `/var/log/cloud-init.log` or `/var/log/cfn-init.log`, to help us debug the instance launch.
 
> [!caution] You can *retrieve the logs by logging in to your instance*, but you must **disable rollback on failure** ([[../102 AWS DVA/CloudFormation RollBacks#Rollbacks | DO_NOTHING]]) or else AWSCloudFormation *deletes the instance after your stack fails* to create.

- Verify that the **instance has a connection to the Internet**. 
	- If the instance is in a VPC, the instance should be able to connect to the Internet through a NAT device if it's is in a private subnet or through an Internet gateway if it's in a public subnet.

## Retaining data on deletes
- You can put a **DeletionPolicy** on any resource to control what happens when the CloudFormation template is deleted.
- `DeletionPolicy=Retain`:
	- Specify on *resources to preserve / backup* in case of CloudFormation deletes
- `DeletionPolicy=Snapshot`:
	- EBS Volume, ElastiCache Cluster, ElastiCache ReplicationGroup, RDS DBInstance, RDS DBCluster, Redshift Cluster
	- Actual instance gets deleted but it makes a snapshot before deleting it.
- `DeletePolicy=Delete` (**default behaviour**):
	- Note: for `AWS::RDS:DBCluster` resources, the *default policy is Snapshot*
	- *Note*: to *delete an S3 bucket we need to first empty the bucket of its content*.

- Example:
	- ![[attachments/Pasted image 20230303101248.png]]

## Miscellaneous
- To protect our stacks we can enable **termination protection** for our stacks.
	- It is similar to termination protection of EC2 instances.
	- When someone tries to delete the stack, it *won't be deleted* and it **won't give an error**.
- Use **`CreationPolicy`** if you want to make sure that the **first creation of ASG is successful**.
- **`UpdatePolicy`** to specify **how CloudFormation handles the update of an ASG**:
	- **`AutoScalingReplacingUpdate`**: This *will create a new ASG*. **Immutable** update.
	- `AutoScalingRollingUpdate`: Instances are created and terminated within the same ASG.
	- `AutoScalingScheduleAction`

> [!question]- A SysOps Administrator deployed an application using AWS CloudFormation. The application runs on Amazon EC2 instances in an *Auto Scaling group behind an Application Load Balancer (ALB)*. A new version of the application must be deployed. *The update must avoid DNS changes and support rollback*. Which solution should the Administrator use to meet the deployment requirements for application update?
> Modify the CloudFormation template to use an AutoScalingReplacingUpdate policy. Update the stack. Perform a second update with the new release.

> [!note] If we DON'T specify an `UpdatePolicy` then it will just update launch template of the ASG and *WON'T do anything to the instances*.

- Using **`DependsOn`** we can specify that an EC2 instance should be created after an RDS database.
	- We **introduce relation** between the resources.
- We can **prevent updates** to stack resources using **StackPolicy**
	- *StackPolicy is a JSON* file and looks like an IAM policy.
	- We have all the same components (Principal, Effect, Action, Resource) as present in the IAM policy.
