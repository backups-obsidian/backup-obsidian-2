---
created: 2023-01-02 20:06
updated: 2023-12-14 19:27
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Providers]] |
|-|

| Next: [[Terraform - Variables]] |
|-|

---
## Terraform Commands
- `terraform apply -auto-approve`: this will run terraform apply *without confirmation*.
- `terraform plan -out <filename>`: save a plan in **binary**
	- Later, we can apply the saved plan, and Terraform will only perform the changes listed in the plan.
	- *Applying a plan file will not prompt us for confirmation*.
	 > [!caution] Terraform plan files can contain sensitive data. **Never commit a plan file to version control**, whether as a binary or in JSON format.
- `terraform apply <planname>`: apply a saved plan
- Terraform provides two arguments to the `plan` and `apply` commands that allow you to **interact with specific resources**: `-replace` and `-target`.
	- `terraform apply -replace resource_type.resource_name`: Cases where using `-replace` is useful.
		- One example of using this would be when a **resource has become unhealthy or stops working in ways that are outside of Terraform's control**.
			- For instance, an error in the EC2 instance's OS configuration could require that the instance be replaced. There is *no corresponding change to your Terraform configuration*, so you want to *instruct Terraform to re-provision the resource using the same configuration*.
		- Replacing a resource is also useful in cases where a *user manually changes a setting on a resource* or when we need to update a provisioning script. 
		- It allows us to rebuild specific resources and avoid a full `terraform destroy` operation on your configuration. The `-replace` flag allows us to target specific resources and avoid destroying all the resources in your workspace just to fix one of them.
		- **In older versions of Terraform, you may have used the `terraform taint` command to achieve a similar outcome. That command has now been deprecated in favor of the `-replace` flag, which allows for a simpler, less error-prone workflow**.
	- `terraform apply -target resource_type.resource_name`: We can target individual resources.
		- There might be a case where we may need to *partially apply configuration* while troubleshooting an error that prevents Terraform from applying our entire configuration at once.
		- An example would be a case where we use random string to generate the tag of an EC2 instance. Now we want to change the length of the random string but we don't want to recreate the EC2 instance. We will only target random in this case.
- `terraform -v`: Getting the *version of providers and terraform* after initializing terraform.
- `terraform providers`: To see a list of all the providers in the current configuration directory.
	- `terraform providers mirror`: to mirror the providers to the local machine.
- `terraform validate`: To check if the syntax of the terraform files.
	- **We need to initialize the terraform configuration directory using `terraform init` before running this command**.
- `terraform fmt`: Format the terraform files.
	- By default, `fmt` scans the current directory for configuration files and formats them according to the HCP canonical style and format. 
	- `terraform fmt -recursive` will scan and format files in sub-directories.
- `terraform state list`: List the state
- `terraform destroy`: **Delete the infrastructure completely**.
	- `terraform apply -destroy`
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
- `terraform graph`: generate a visual representation in the DOT format which then needs to be passed through a visualization tool.
- `terraform force-unlock`: Manually unlock the state for the defined configuration.
	- The behaviour of this lock is dependent on the backend being used. 
	- *Local state files CANNOT be unlocked by another process*.
- `terraform init -migrate-state`: Used to migrate state when terraform's configuration backend changes.