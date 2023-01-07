---
created: 2023-01-02 20:06
updated: 2023-01-06 09:40
---
---
**Links**: [[113 Terraform Index]]

---
## HCL/Terraform Commands
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

### Other Commands
- `terraform validate`: To check if the syntax of the terraform files.
- `terraform fmt`: Format the terraform files.
- `terraform show -json`: To show the contents of the state file in json format.
- `terraform providers`: To see a list of all the providers in the current configuration directory.
- `terraform output`: Prints all the output variables.
	- `terraform output variable_name`: Printing a specific variable. 
- `terraform refresh`: Used to sync terraform with the real world architecture.
	- Useful when a manual update is made to the resources controlled by terraform.
	- *This will only modify the state file*.
	- **This is automatically run by `terraform plan` and `terraform apply`**.
		- We can bypass this behaviour by using `-refresh=false` with the above commands.
- `terraform graph`: Create a visual representation of dependencies.
	- We need to pass it through a graphing tool.