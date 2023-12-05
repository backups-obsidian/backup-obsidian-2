---
created: 2023-12-04 19:22
updated: 2023-12-04 20:03
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Modules]] |
|-|

| Next: [[Terraform - Functions]] |
|-|

---
## Managing Multiple Environments
- Imagine a scenario where you have multiple environments (dev and prod).
	- Both of them refer to the same Terraform configuration.
	- Now since both of them refer to the same Terraform configuration there is chance that we might destroy the production environment when destroying the development environment.

### Solution 1: Creating Separate Directories
- By creating **separate directories for each environment**, we can shrink the blast radius of our Terraform operations. 
	- Terraform *stores our state files on disk in their corresponding configuration directories*. 
	- Terraform *operates only on the state and configuration in the working directory by default*.
- One problem with this approach is that it relies on duplicate Terraform code. 
	- The separate directory method runs the risk of **creating drift between the environments over time**.

### Solution 2: Creating Workspaces
- Workspace-separated environments use the **same Terraform code but have different state files**, which is useful if we want your environments to stay as similar to each other as possible, for example if we are providing development infrastructure to a team that wants to simulate running in production.
- However, we **must manage your workspaces in the CLI** and be *aware of the workspace we are working in to avoid accidentally performing operations on the wrong environment*.
- All Terraform configurations start out in the **`default` workspace**.
- Workspace specific commands:
	- *Listing the workspaces*: `terraform workspace list`.
	- *Creating a new workspace*: `terraform workspace new <workspace-name>`.
	- *Selecting a workspace*: `terraform workspace select <workspace-name>`
- We apply the Terraform configuration by switching to the required workspace.
- When we use the *default workspace with the local backend, our `terraform.tfstate` file is stored in the root directory of our Terraform project*.
- When we use **add additional workspaces our state location changes**; 
	- Terraform internals manage and store state files in the directory **`terraform.tfstate.d`**

```hcl hl:9,11,13 title:"Sample directory structure with multiple workspaces" fold
.
├── README.md
├── assets
│   └── index.html
├── dev.tfvars
├── main.tf
├── outputs.tf
├── prod.tfvars
├── terraform.tfstate.d
│   ├── dev
│   │   └── terraform.tfstate
│   ├── prod
│   │   └── terraform.tfstate
├── terraform.tfvars
└── variables.tf
```