---
created: 2023-12-03 14:50
updated: 2023-12-03 14:56
---
---
**Links**: [[113 Terraform Index]]

---
## Terraform Cloud
- Terraform Cloud allows **teams** to easily *version, audit, and collaborate on infrastructure changes*. 
- It **also securely stores variables, including API tokens and access keys**, and provides a safe, stable environment for long-running Terraform processes.

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
- Run `terraform init` to *re-initialize your configuration and migrate your state file to Terraform Cloud*.
	- Once the state file has been migrated *remove the local state file*.