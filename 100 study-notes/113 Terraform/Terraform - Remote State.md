---
created: 2023-01-09 22:37
updated: 2023-01-11 17:31
---
---
**Links**: [[113 Terraform Index]]

---
## [[Terraform - State | Intro to Terraform State]]
## Remote State
- When we use `terraform apply` for the first time a file `terraform.tfstate` is created in the configuration directory.
- Benefits of terraform state:
	- Mapping terraform state to the real world.
	- *Tracking metadata* such as dependencies which allows terraform to create and delete resources in the correct order.
	- *Improving the performance of terraform operations while working with large configuration files*.
	- It allows for *collaboration* between different members of the team.
		- Local state files are not good for collaboration.

> [!note]- It is also **NOT a good idea to store the state file in a version control system** since it contains sensitive information.
> Also if the state file is stored in a git repo and when we work as a team it is *essential that only one person runs the operation against the same configuration at any given time*.

- In case of a single source of truth for the state file terraform locks the state file.
	- This features is also known as **state locking**.
	- *This ensures that state file doesn't get corrupted by multiple operations trying to update it at the same time*.
		- ![[attachments/Pasted image 20230111101911.png]]
	- Version control systems like GitHub do not support state locking.

- Because of all these reasons it is advised to store the state file in a remote secured shared storage.
- When we configure a remote backend terraform will *automatically load the state file every time it is required by the terraform operation*.
	- ![[attachments/Pasted image 20230111102254.png]]
	- It will also *upload the updated terraform state file after every apply*.
	- A lot of remote state options allow for state locking.
	- Remote state backends also provide encryption for security.

### Remote backends using S3
- For this we will need an *S3 bucket* and a **DynamoDB table for implementing state locking**.
	- ![[attachments/Pasted image 20230111102450.png]]
- Configuring remote backend using the terraform block:
	- ![[attachments/Pasted image 20230111104516.png]]
	- We provide a dynamodb table for achieving state locking.
	- We need to run `terraform init` to initialise the new backend.
	- Every time we run `terraform apply` command state file will be downloaded to memory and will be uploaded to remote state after updation.
	- State file will not be stored in the local configuration directory anymore.

## Terraform state manipulation
- Syntax of terraform state command: `terraform state <sub command> [options] [args]`
- `terraform state list`: This will **list all the resources** recorded within the terraform state file.
	- ![[attachments/Pasted image 20230111110330.png]]
- To get **more information about the resources** we can use the `terraform state show <resrource>`
	- This will *show all the attributes of the respective resource*.
	- ![[attachments/Pasted image 20230111110524.png]]
- `terraform state mv SOURCE DESTINATION`: This is used to move items in a terraform state file.
	- It can be used to *move resources within the same state file* (essentially **renaming the resource**).
	- It can be used to *move resources from one state file to another state file* maintained by another configuration directory.
	- Example renaming the dynamodb table
		- ![[attachments/Pasted image 20230111111512.png]]
		- **After this we must manually rename the resource in the configuration file**.
- *Viewing remove state*: 
	- `terraform state pull` and then pipe the output to json parsing tools like `jq`.
- Removing items from terraform state `terraform state rm <resource-address>`.
	- This is used when we no longer wish to manage one or more resources using terraform  configuration and state.
		- ![[attachments/Pasted image 20230111111455.png]]
	- After removing the resource from the state file we need to manually *remove it from the configuration file*.
	- **Resources removed from the state file are NOT actually destroyed**. It is only removed from the terraform management.