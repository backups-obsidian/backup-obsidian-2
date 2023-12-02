---
created: 2023-01-02 20:06
updated: 2023-12-02 14:12
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - How Terraform works?]] |
|-|

| Next: [[Terraform - Variables]] |
|-|

---
## Terraform Commands
- `terraform plan -out <filename>`: save a plan in **binary**
	- Later, we can apply the saved plan, and Terraform will only perform the changes listed in the plan.
	- *Applying a plan file will not prompt us for confirmation*.
	 > [!caution] Terraform plan files can contain sensitive data. **Never commit a plan file to version control**, whether as a binary or in JSON format.
- `terraform apply <planname>`: apply a saved plan
- Terraform provides two arguments to the `plan` and `apply` commands that allow you to **interact with specific resources**: `-replace` and `-target`.
	- `terraform apply -replace resource_type.resource_name`: One example of using this would be when a **resource has become unhealthy or stops working in ways that are outside of Terraform's control**. 
		- For instance, an error in the EC2 instance's OS configuration could require that the instance be replaced. 
		- There is *no corresponding change to your Terraform configuration*, so you want to *instruct Terraform to reprovision the resource using the same configuration*.
	- `terraform apply -target resource_type.resource_name`: We can target individual resources.
		- There might be a case where we may need to *partially apply configuration* while troubleshooting an error that prevents Terraform from applying our entire configuration at once. 
		- This type of error may occur when a target API or Terraform provider error leaves your resources in an invalid state that Terraform cannot resolve automatically.
- `terraform -v`: Getting the *version of providers and terraform* after initializing terraform.
- `terraform providers`: To see a list of all the providers in the current configuration directory.
- `terraform validate`: To check if the syntax of the terraform files.
- `terraform fmt`: Format the terraform files.
- `terraform state list`: List the state
- `terraform destroy`: **Delete the infrastructure completely**.
- `terraform show`: *to provide human-readable output from a **state or plan** file*.
	- `terraform show <planfile>`: To see the contents of a plan file
	- `terraform show -json`: To show the contents of the state file in json format.
	- `terraform show -json <planfile>`: To show the contents of the plan file in json format.
- `terraform output`: Prints all the output variables.
	- `terraform output variable_name`: Printing a specific variable. 
- `terraform output -json`: query all of our outputs in JSON format.
- `terraform refresh`: Used to sync terraform with the real world architecture.
	- Useful when a manual update is made to the resources controlled by terraform.
	- *This will only modify the state file*.
	- **This is automatically run by `terraform plan` and `terraform apply`**.
		- We can bypass this behaviour by using `-refresh=false` with the above commands.
