---
created: 2023-12-09 09:26
updated: 2023-12-17 11:02
---
---
**Links**: [[113 Terraform Index]]

---
- Given a resource block we CANNOT use `for_each` and `count` together.
- `lifecycle` meta argument is NOT supported by the `data` block.
- A label after the variable keyword should be unique among all the variables in the same module.
- `for_each` works with a `map` and `set` of strings.
- A module can call other modules using the `module` block.
- In the UI and VCS workflow, every workspace is associated with a specific branch of a VCS repo of Terraform configurations. 
	- Terraform Cloud registers webhooks with our VCS provider when we create a workspace, then automatically queues a Terraform run whenever new commits are merged to that branch of workspace's linked repository.
- We CANNOT use `dynamic` blocks to generate `meta-argument` blocks such as `lifecycle` and `provisioner` blocks.
- `terraform show` displays the whole state, `terraform state show <resource_type>.<resource_name>` is used to show detailed information about a specific resource.
- We can use version constraints in Provider requirements, `required_version` in the the terraform block and modules.
- Modules are also of 3 types: Verified, Community & Official.
- Lifecycle arguments present: `ignore_changes`, `prevent_destroy`, `create_before_destroy`.
- `[for o in var.list : o.id]` is same as `var.list[*].id`
- Every Terraform configuration has at least one module, known as its root module, which consists of the resources defined in the `.tf` files in the main working directory.
- Logging can be enabled *separately for terraform itself* and the *provider plugins* using the `TF_LOG_CORE` or `TF_LOG_PROVIDER` environment variables. 
	- These take the same level arguments as `TF_LOG`, but only activate a subset of the logs.
- For storing secrets 
	- use backend that supports encryption 
	- use secret stores like Vault
	- Environment Variables
	- Encrypted files
- What will happen if Terraform isn't able to obtain acceptable versions of external dependencies, or if it doesn't have an acceptable version of itself?
	- It won't proceed with any plans, applies, or state manipulation actions.
- `local-only` data sources:
	- It is *re-calculated each time* a new plan is created
	- It is the same as all other data sources, but their *result data exists only temporarily during a Terraform operation*.
- Terraform assumes an empty default configuration for any provider that is not explicitly configured.
- Splat expression is valid of `list`, `set` and `tuple`.
	- It is NOT valid for `map`
- The `-chdir` option instructs Terraform to change its working directory to the given directory before running the given subcommand. 
	- This means that any files that Terraform would normally read or write in the current working directory will be read or written in the given directory instead.
- The `default` argument within the `variable` block should satisfy the following conditions:
	- *`default` argument requires a `literal` value*
		- *It CANNOT reference other objects in the configuration*.
	- `default` value will be used if no value is set when calling the module or running Terraform.
	- If present, the variable is considered to be optional.
- When multiple arguments with single-line values appear on consecutive lines at the same nesting level, HashiCorp recommends that we *align the equals signs*.
- Hashicorp recommends using **two spaces** for indentation when writing Terraform code.
- What is the downside to using Terraform to interact with sensitive data, such as reading secrets from Vault?
	- Interacting with Vault from Terraform causes *any secrets that we read and write to be persisted in both Terraform's state file and in any generated plan files*.
- The prefix `-/+` means that Terraform will destroy and recreate the resource, rather than updating it in-place. 
	- Some attributes and resources can be updated in-place and are shown with the `~` prefix.
- The best and easiest way for Terraform to read and write secrets from HashiCorp Vault is by using the Vault Provider.
- There are multiple ways to provide sensitive values when using Terraform. However, sensitive information provided in our configuration can be written to the state file, which is not desirable. Is there a method below that will not result in sensitive information being written to the state file?
	- When using sensitive values in our Terraform configuration, any configuration will result in the sensitive value being written to the state file.
- Terraform cloud agents only available for Terraform Cloud for Business.
- Modules on the public Terraform Registry can be referenced using a registry source address of the form `<NAMESPACE>/<NAME>/<PROVIDER>`.

```hcl title:"Accessing credentials from a vault" fold
provider "aws" {
	region = us-east-1
	access_key = data.vault_aws_access_credentials.creds.access_key
	secret_key = data.vault_aws_access_credentials.creds.secret_key
}
```

- `terraform plan -refresh-only` can be used to detect a configuration drift.
- Providers can be installed using multiple methods, including downloading from a Terraform public or private registry, the official HashiCorp releases page, a local plugins directory, or even from a plugin cache.
- `terraform init` caches the source code for modules in the local directory so they can be used.
- `terraform workspace show` to list the current workspace.
- Terraform cloud private module registry can be used to restrict our team members to specific modules that are approved using the organization's security team.
- `terraform console` locks the state.
- `slice` is not a valid string function in terraform.
- We should use `force-unlock` command to manually unlock the state when automatic unlocking failed.
- You have declared a variable called `var.list` which is a list of objects that all have an attribute `id`. The following will produce a *list of ids*.
	- `var.list[*].id`
	- `[ for o in var.list : o.id ]`
- *A single remote backend can map to multiple remote workspaces*.
	- The remote backend can work with either a single remote Terraform Cloud workspace, or with multiple similarly-named remote workspaces (like networking-dev and networking-prod). *The workspaces block of the backend configuration determines which mode it uses*:
		- To use a single remote Terraform Cloud workspace, set workspaces.name to the remote workspace's full name (like networking-prod).
		- To use multiple remote workspaces, set workspaces.prefix to a prefix used in all of the desired remote workspace names.
			- For example, set prefix = "networking-" to use Terraform cloud workspaces with names like networking-dev and networking-prod. 
			- This is helpful when mapping multiple Terraform CLI workspaces used in a single Terraform configuration to multiple Terraform Cloud workspaces.
- **`description` in output and input variables are NOT stored in state file**.
	- It is only used for documentation.
	- The values of the variables are stored but not the description.
- Terraform providers may not always be installed from the internet.
- `terraform refresh` is helpful for detecting drift.