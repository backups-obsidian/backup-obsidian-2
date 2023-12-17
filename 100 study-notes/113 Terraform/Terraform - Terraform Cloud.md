---
created: 2023-12-03 14:50
updated: 2023-12-16 10:29
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Miscellaneous]] |
|-|

---
## Terraform Cloud
- Terraform Cloud allows **teams** to easily *version, audit, and collaborate on infrastructure changes*. 
- It **also securely stores variables, including API tokens and access keys**, and provides a safe, stable environment for long-running Terraform processes.
- In Terraform Cloud, the **resources are organized by workspaces**, which contain our resource definitions, environment and input variables, and state files. 
	- *A Terraform operation occurs within a workspace*, and Terraform uses the configuration and state for that workspace to modify our infrastructure.
- Terraform Cloud supports **three workflows** for our Terraform runs:
	- The *CLI-driven workflow*, which uses Terraform's standard CLI tools to execute runs in Terraform Cloud.
		- We initiate Terraform operations in *our Terminal*, and use *Terraform Cloud's ephemeral remote execution* environments for our operations*.
	- The *UI/Version Control System(VCS)-driven workflow*, in which **changes pushed to version control repositories trigger runs in the associated workspace**.
		- We must configure VCS access and create our workspace, then associate it with a repository containing our Terraform configuration. 
		- We can then configure your workspace to *create speculative plans for any Pull Requests*, which allows our team to review how the changes would modify infrastructure. 
		- Any *merges to our main branch then trigger Terraform runs to implement our changes*.
		> [!note] When using the VCS-driven workflow for Terraform Cloud, we *DONOT need to define the `cloud` block in our configuration*.
	- The API-driven workflow, which allows you to create tooling to interact with the Terraform Cloud API programmatically.
- **Sentinel is a Policy as code framework** for HashiCorp Enterprise products.
	- **We use sentinel to create and enforce policies**.
- Terraform Cloud is the same thing as Terraform Enterprise.
- *In Terraform cloud a workspace can be mapped to 1 VCS repo*.

> [!note] Terraform Cloud workspaces are NOT the same as Terraform CLI Workspaces.

### Using variables
- There are **4 ways** of using variables in Terraform Cloud:
	- **workspace specific variables** 
	- **variable sets: they can be used across workspaces**
		- We can define both **input variables and environment variables** in variable sets. 
		- Input variables define the values for variables we reference in our configuration, while *environment variables typically store provider credentials or modify Terraform's behaviour, such as logging verbosity*.
	- **per-run variables**: we set per run variables in Terraform  cloud using command line flags. 
		- When using the CLI-driven workflow for Terraform Cloud, **any variables passed using the `-var` flag will override workspace-specific variables**. 
		- However, *Terraform Cloud will not save the new value in our workspace unless we update the variable in the UI*. 
		- This feature can be useful when we want to test or temporarily apply a change that we expect to revert on the next apply, so our stored variable values continue to reflect the desired eventual configuration.
	- **`*.auto.tfvars`**: the `auto.tfvars` files will have the **lowest precedence in a workspace**, and any workspace specific variables will override the values defined in this file.

### How to use Terraform cloud
```hcl title:"Code changes to use terraform cloud" fold
terraform {
  cloud {
    organization = "organization-name"
    workspaces {
      name = "learn-tfc-aws" 
    }
  }
}
```

- Login to terraform cloud using `terraform login`.
	- If login is successful, Terraform will store the token in plain text in the following file for use by subsequent commands: `/Users/<USER>/.terraform.d/credentials.tfrc.json`
	- API and CLI access are managed with API tokens, which can be generated in the Terraform Cloud UI.
- Run `terraform init` to *re-initialize your configuration and migrate your state file to Terraform Cloud*.
	- Once the state file has been migrated *remove the local state file*.
-  Terraform cloud *workspaces specified in the configuration file will be automatically created if not present*.
- **Every Terraform Cloud workspace belongs to a project**, which is a *group of workspaces*.
	- When we create our workspace using the CLI workflow, Terraform Cloud adds the workspace to our organization's **Default** project unless we set the `project` argument in our configuration's `cloud` block.
	- Projects help us organize our workspaces into groups, making it easier to find workspaces in large Terraform Cloud organizations.

> [!caution] Deleting a workspace in Terraform cloud DOES NOT destroy its infrastructure.

> [!important]+ Workspaces, managed with the terraform workspace command, **ISN'T the same thing** as Terraform Cloud's workspaces. 
> - *Terraform Cloud workspaces* act more like completely **separate working directories**.
> - *CLI workspaces* (OSS) are **just alternate state files**.

### Sentinel
- Why might users want to utilize **Sentinel or OPA** (Open Policy Agent) with Terraform Cloud in their infrastructure workflow?
	- Sentinel and OPA enable *automated policy checks* to enforce *compliance standards* before applying changes to production environments.
	- Organizations can *enforce resource naming conventions* or *approved machine images* for improved consistency and clarity.
	- To provide *real-time feedback on potential security risks in Terraform configurations* during the development process.
	- Sentinel and OPA can enhance security by *preventing unauthorized changes* to your managed infrastructure.
	- Sentinel and OPA policies are *defined as code*, which means they can be stored in version control alongside our Terraform configurations.

> [!note] Sentinel policy evaluations **occur after Terraform completes the plan and after both run tasks and cost estimation**. 
> - This order lets us write Sentinel policies to restrict costs based on the data in the cost estimates.

- OPA policy evaluations are slightly different and occur after Terraform completes the plan and after any run tasks. 
	- *Unlike Sentinel policies, Terraform Cloud evaluates OPA policies immediately before cost estimation*.

> [!note] Sentinel policies are written using the **Sentinel language**.