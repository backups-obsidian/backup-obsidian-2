---
created: 2023-01-02 21:55
updated: 2023-12-02 13:06
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Variables]] |
|-|

| Next: [[Terraform - State]] |
|-|

---
## Basics
### Providers
- After we write a terraform configuration file the first thing we have to do is to run `terraform init`.
	- *When we run `terraform init` in the directory containing the configuration files, terraform **downloads and installs plugins for the providers** used in the configuration*.
	- It shows the version of the plugin being installed.
- `terraform init` is a safe command which **can be run as many times as required without impacting the architecture**.
- There are 3 different tiers of providers:
	- *Official*: Owned and maintained by hashicorp. This includes the major cloud providers like AWS, Azure and GCP.
	- *Verified*: Owned and maintained by a 3rd party technology company.
	- *Community*: Published and maintained by individual contributors.
- Once we run `terraform init` the plugins are downloaded to a hidden directory `.terraform/plugins` in the directory containing the configuration files.
	- We can inspect this to find all the plugins installed.
- If the registry is not provided then terraform uses the *default registry of `registry.terraform.io`* to download the plugins.

> [!note] By default terraform *installs the latest version of the provider*.

- Terraform will consider any file with `.tf` extension for configuration changes.
- A single configuration file can also have as many configuration blocks as we need.
- We can have multiple providers in the same configuration directory.
	- **Each time we add a new provider in the configuration file we have to run `terraform init`**.

### Resource attributes
- **There are bound to be multiple resources that are dependent on each other**.
- Example:

```terraform
resource "local_file" "pet" {
	filename = var.filename
	content = "My favorite pet is ${random_pet.my-pet.id}"
}

resource "random_pet" "my-pet" {
	prefix = var.prefix
	separator = var.separator
	length = var.length
}
```

- In the above example I am using `id` returned by the `random_pet` resource.
	- Every resource might have 0 or more output variables.
	- `random_pet` resource has one output variable called `id`

### Resource Dependencies
- In the above example due to the dependency terraform knows to create `random_pet` resource first and then create the `local_file` resource. 
- **When resources are deleted they are deleted in the reverse order**. 
	- `local_file` first and then the `random_pet`
- This type of dependency is known as the **implicit dependency**. 
	- We are not specifying which resources are dependent on what, terraform figures it out itself.
 - We can specify **dependency explicitly using `depends_on`** argument.
	 - This is necessary *only when a resource relies on some other resource indirectly* and it *does not make use of a reference expression*.

```terraform
resource "local file" "pet" {
	filename = var.filename
	content = "My favorite pet is Mr.Cat"
	depends_on = [
		random_pet.my-pet
	]
}

resource "random pet" "my-pet" {
	prefix = var.prefix
	separator = var.separator
	length = var.length
}
```
