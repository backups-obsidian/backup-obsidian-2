---
created: 2023-01-09 22:37
updated: 2023-12-03 18:09
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Lifecycle Rules]] |
|-|

| Next: [[Terraform - Functions]] |
|-|

---
## Modules
- We can split the single terraform configuration into multiple configuration files and it will work the same way.
	- Terraform will consider all files with `.tf` extension as configuration files.
	- This approach *limits the reusability of the code*.
- Using modules:
	- ![[attachments/Pasted image 20230114184711.png]]
	- We can also use absolute path to the child module.
	- Here `dev-webserver` is the logical name of the module. 

### Creating and Using modules
- Creating the module
	- ![[attachments/Pasted image 20230114185517.png]]
	- The variables will come from the terraform file using the modules
- Using the module
	- ![[attachments/Pasted image 20230114185753.png]]
	- Providing the bucket variable name would have overwritten the default bucket value
	- *We run `terraform init` and `terraform apply` in the us payroll directory and not the module*.
	- When we run `terraform init` it downloads our module from source along with the AWS plugin.
	- Referring the payroll db:
		- ![[attachments/Pasted image 20230114190254.png]]

- Modules are comparable to libraries or packages in other programming languages.
- Having certain components of the infra fixed (like in the above case DynamoDB table name in different regions) ensures provisioning process maintains a standard configuration.

### Using modules from the public terraform registry
- The module that we built above is a local module.
- **The terraform registry is used to store provider plugins (like AWS) and modules**.
- The modules can be categorised into 2 types:
	- Verified
	- Community
- The instructions on how to use the terraform modules can be found on the registry.
- To download the module we can use `terraform get` after using it in our configuration file.
- *It is a good idea to specify the version of the module while using it*.
	- **Not specifying the version will always download the latest version from the registry**.