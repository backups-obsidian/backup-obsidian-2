---
created: 2023-01-11 16:24
updated: 2023-12-01 15:07
---
---
**Links**: [[113 Terraform Index]]

---
## Provisioners
- Provisioners provide a way for us to *carry out tasks like running commands or scripts on remote resources or on the local machine*.
- Provisioner block directly *goes inside the resource block*.

> [!note]- For the provisioner script/commands to work there should be **network connectivity between the  local machine (running terraform) and the remote instance**.
> ![[attachments/Pasted image 20230111162504.png]]

- Example of *remote exec* provisioner:
	- ![[attachments/Pasted image 20230111162837.png]]

- *Local exec* provisioner is used to run *tasks in the local machine* where we are running terraform and *NOT on the resources that are created by terraform*.
	- This is handy when we want to gather some information and write to a file locally.
	- For example storing the public IP address of an EC2 instance that was just provisioned.
		- ![[attachments/Pasted image 20230111165224.png]]

> [!note] By default **provisioners are run after the resource is created**. This is also known as the *create time provisioner*.

- We can also run a provisioner before a resource is destroyed. This is known as *destroy time provisioner*.
	- This is done using the `when` command
	- ![[attachments/Pasted image 20230111165510.png]]
- Another *default behaviour of provisioner is that if the command fails then terraform apply also errors out*.
	- This is set using `on_failure = fail` (default, need not be specified) but we can make terraform continue execution by setting `on_failure = continue`
	- When a `terraform apply` fails due to `on_failure = fail` terraform resource is marked as *tainted*.

- As a **best practice terraform recommends using provisioners as the last resort**.
	- For example for running scripts in EC2 instances use `user_data` instead of using terraform remote exec provisioner.
	- Keep post processing tasks to a minimum and build custom images that will have all the required software.
		- *This can be done using templating tools like Packer in a declarative way*.