---
created: 2023-01-02 20:06
updated: 2023-01-04 14:24
---
---
**Links**: [[113 Terraform Index]]

---
## HCL
### Basics
- Creating a simple file
	- ![[attachments/Pasted image 20230102201046.png]]

```terraform
resource "local_file" "pet" {
	filename = "/root/pets.txt"
	content = "Hello there"
}
```
- Argument `filename` is compulsory for resource type `local_file`.
	- ![[attachments/Pasted image 20230102212727.png]]
- Some examples of AWS resources:
	- ![[attachments/Pasted image 20230102212056.png]]
	- ![[attachments/Pasted image 20230102212118.png]]

- A terraform workflow consists of 4 steps:
	- Write the configuration file.
	- Next run the `terraform init` command.
	- Review the execution plan using the `terraform plan` command.
	- Apply the changes using the `terraform apply` command.
- To delete the infrastructure completely run the `terraform destroy` command.
- Getting the version of modules and terraform after initialising terraform
	- `terraform -v`
- `terraform show`: the terraform show command is used *to provide human-readable output from a state or plan file*.