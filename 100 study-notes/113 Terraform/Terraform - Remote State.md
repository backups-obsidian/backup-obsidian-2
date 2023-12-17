---
created: 2023-01-09 22:37
updated: 2023-12-14 19:23
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - State]] |
|-|

| Next: [[Terraform - State Manipulation]] |
|-|

---
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
- Some **supported remote backends are `S3`, `local`, `kubernetes` and `Consul`**. If there are multiple team members then go for either S3, kubernetes or Consul backend.
	- *GitHub as a backend is NOT supported which means it DOESNOT support state locking*.
	- When using GitHub as the remote backend the state file could become corrupted, if multiple users are trying to make changes at the same time.

- Because of all these reasons it is advised to store the state file in a remote secured shared storage.
- When we configure a remote backend terraform will *automatically load the state file every time it is required by the terraform operation*.
	- ![[attachments/Pasted image 20230111102254.png]]
	- It will also *upload the updated terraform state file after every apply*.
	- A lot of remote state options allow for state locking.
		- **It will NOT use state locking if it isn't supported by the backend**.
	- Remote state backends also provide encryption for security.

> [!note] By default if no backend is specified then terraform defaults to local backend.

 
### Remote backends using S3
- For this we will need an *S3 bucket* and a **DynamoDB table for implementing state locking**.
	- ![[attachments/Pasted image 20230111102450.png]]
- Configuring remote backend using the terraform block:
	- ![[attachments/Pasted image 20230111104516.png]]
	- We provide a dynamodb table for achieving state locking.
	- We need to run `terraform init` to initialise the new backend.
	- Every time we run `terraform apply` command state file will be downloaded to memory and will be uploaded to remote state after updation.
	- State file will not be stored in the local configuration directory anymore.
- The S3 backend supports encryption at rest when the `encrypt` option is enabled.