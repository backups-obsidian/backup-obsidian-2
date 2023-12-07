---
created: 2023-12-02 09:53
updated: 2023-12-06 19:15
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Introduction]] |
|-|

| Next: [[Terraform - Commands]] |
|-|

---
## How Terraform Works?
- Terraform works in *3 phases: `init`, `plan` and `apply`*.
	- During the `init` phase terraform **configures the backend**, **installs all providers (`./terraform/plugins`) and modules (`./terraform/modules`)** referred to in your configuration, and *creates a version lock file* if one DOESN'T already exist.
		- `init` command is also used to *change your workspace's backend* and *upgrade your workspace's providers and modules*.
	- During the `plan` phase terraform drafts a plan to get to the target state.
		- Terraform creates the plan by comparing out Terraform configuration to the state of our infrastructure.
	- During the `apply` phase terraform makes the necessary changes to the target environment to bring it to the desired state.
- **Every object that terraform manages is called a resource**.
	- It can be a compute instance or a database server or any other component
- Terraform records the state of the infrastructure as seen in the real world. 
	- Based on this it can determine what actions to take for updating resources for a particular platform.
	- This ensures that the entire infrastructure is in the defined state at all times.
	- *Terraform can read attributes of existing infrastructure components*.
	- *Terraform can manage resources created by other IaC tools*.
- **A basic terraform workflow** consists of 4 steps:
	- Write the configuration file to create resources.
		- In the below example **`local_file` is the resource type, `local` is the provider, `pet` is the name of the resource and `filename` and `content` are arguments of that resource**.
			- ![[attachments/Pasted image 20230102201046.png]]
			- Different providers have different arguments and different required values.
			- Argument `filename` is compulsory for resource type `local_file`.
		- Some examples of AWS resources using `aws` provider:
			- ![[attachments/Pasted image 20230102212056.png]]
			- ![[attachments/Pasted image 20230102212118.png]]
	- Running the `terraform init` command.
	- *Review the execution plan* using the `terraform plan` command.
	- *Apply the changes* using the `terraform apply` command.

> [!important] Terraform loads all configuration files within a directory and appends them together, so *any resources or providers with the same name in the same directory will cause a validation error*.

### Understanding `init`
- `terraform init` is a safe command which **can be run as many times as required without impacting the architecture**.
- If the registry is not provided then terraform uses the *default registry of `registry.terraform.io`* to download the plugins.
- **Each time we add a new provider in the configuration file we have to run `terraform init`**.
- When we initialize a workspace, Terraform will attempt to download the provider versions specified by the workspace's lock file. 
	- *If the lock file DOES NOT exist, Terraform will use the `required_providers` block to determine the provider version and create a new lock file (`.terraform.lock.hcl`)*.
	- If *neither exists*, Terraform will search for a matching provider and *download the latest version*.

```hcl file:"Example terraform.tf file for specifying the terraform version and the version of the providers" fold
terraform {
  required_version = "~> 1.6" # specifies the terraform version to be used
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "5.7.0"
    }
    random = {
      source  = "hashicorp/random"
      version = "3.0.1"
    }
  }
}
```

^0b9580

- **The `version` attribute for a provider is optional**, but it is recommend to use it to constrain the provider version so that Terraform does not install a version of the provider that does not work with our configuration.
- We should *include the lock file in your version control repository* to ensure that Terraform uses the same provider versions across your team and in ephemeral remote execution environments.
- If we change the versions of providers in the  configuration's `required_providers` block, then we have to to re-initialize your configuration using the `-upgrade` flag.
	- `terraform init -upgrade`
- **Terraform uses the `.terraform` directory to store the project's providers and modules**.
	- Terraform automatically manages the `.terraform` directory. 
	- *DONOT check it into version control, and do not directly modify this directory's contents*.

### Understanding `plan`
- We can create a json representation of the plan using:
	- `terraform plan -out tfplan`
	- `terraform show -json tfplan | jq > tfplan.json`
- When we create a plan it has **4 main sections**:
	- **`configuration`**: snapshot of your configuration at the time of the `terraform plan`. 
		- This configuration snapshot *captures the versions of the providers recorded in your `.terraform.lock.hcl` file*, ensuring that you use the same provider versions that generated the plan to apply it.
	- **`resource_changes`**: The `resource_changes` array includes the following fields:
		- The `action` field captures the action taken for the resource.
		- The `before` field captures the *resource state prior to the run*. 
		- The `after` field captures the state to define for the resource.
		- The `after_unknown` field captures the list of values that will be computed or determined through the operation and sets them to `true`.
		- The `before_sensitive` and `after_sensitive` fields capture a list of any values marked `sensitive`. Terraform will use these lists to determine which output values to redact when you apply your configuration.
	- **`planned_values`**: It is a report of the *differences between the "before" and "after" values of your resources*, showing us the planned outcome for a run that would use this plan file.
	- **`prior_state`**: The object *captures the state file* exactly as it was prior to the plan action. This section will not be present if we are creating a plan file and there is no state file.

```json title:"sample plan file" fold
{
  "format_version": "1.2",
  "terraform_version": "1.6.5",
  "variables": {
    "filename": {
      "value": "./testing"
    }
  },
  "planned_values": {
    "root_module": {
      "resources": [
        {
          "address": "local_file.test",
          "mode": "managed",
          "type": "local_file",
          "name": "test",
          "provider_name": "registry.terraform.io/hashicorp/local",
          "schema_version": 0,
          "values": {
            "content": "Cotent of the file",
            "content_base64": null,
            "directory_permission": "0777",
            "file_permission": "0777",
            "filename": "./testing",
            "sensitive_content": null,
            "source": null
          },
          "sensitive_values": {}
        }
      ]
    }
  },
  "resource_changes": [
    {
      "address": "local_file.test",
      "mode": "managed",
      "type": "local_file",
      "name": "test",
      "provider_name": "registry.terraform.io/hashicorp/local",
      "change": {
        "actions": [
          "create"
        ],
        "before": null,
        "after": {
          "content": "Cotent of the file",
          "content_base64": null,
          "directory_permission": "0777",
          "file_permission": "0777",
          "filename": "./testing",
          "sensitive_content": null,
          "source": null
        },
        "after_unknown": {
          "content_base64sha256": true,
          "content_base64sha512": true,
          "content_md5": true,
          "content_sha1": true,
          "content_sha256": true,
          "content_sha512": true,
          "id": true
        },
        "before_sensitive": false,
        "after_sensitive": {
          "sensitive_content": true
        }
      }
    },
    {
      "address": "module.file_creation.local_file.test",
      "module_address": "module.file_creation",
      "mode": "managed",
      "type": "local_file",
      "name": "test",
      "provider_name": "registry.terraform.io/hashicorp/local",
      "change": {
        "actions": [
          "delete"
        ],
        "before": {
          "content": "This is a test file",
          "content_base64": null,
          "content_base64sha256": "4tD+FYWmPsYAnIAW/43aixdxmmN0BaTiPA/4EzkUgkk=",
          "content_base64sha512": "v+e5n7l7oySa8IzEPZv9ObIMby6XAQtZKEBtGVi0cB0vBTGH2lIsTIeN0FZ+eT2e1xBJBc2N89H2+6yeIMitkQ==",
          "content_md5": "0b26e313ed4a7ca6904b0e9369e5b957",
          "content_sha1": "91b7b0b1e27bfbf7bc646946f35fa972c47c2d32",
          "content_sha256": "e2d0fe1585a63ec6009c8016ff8dda8b17719a637405a4e23c0ff81339148249",
          "content_sha512": "bfe7b99fb97ba3249af08cc43d9bfd39b20c6f2e97010b5928406d1958b4701d2f053187da522c4c878dd0567e793d9ed7104905cd8df3d1f6fbac9e20c8ad91",
          "directory_permission": "0777",
          "file_permission": "0777",
          "filename": "./testing",
          "id": "91b7b0b1e27bfbf7bc646946f35fa972c47c2d32",
          "sensitive_content": null,
          "source": null
        },
        "after": null,
        "after_unknown": {},
        "before_sensitive": {
          "sensitive_content": true
        },
        "after_sensitive": false
      },
      "action_reason": "delete_because_no_resource_config"
    }
  ],
  "prior_state": {
    "format_version": "1.0",
    "terraform_version": "1.6.5",
    "values": {
      "root_module": {
        "child_modules": [
          {
            "resources": [
              {
                "address": "module.file_creation.local_file.test",
                "mode": "managed",
                "type": "local_file",
                "name": "test",
                "provider_name": "registry.terraform.io/hashicorp/local",
                "schema_version": 0,
                "values": {
                  "content": "This is a test file",
                  "content_base64": null,
                  "content_base64sha256": "4tD+FYWmPsYAnIAW/43aixdxmmN0BaTiPA/4EzkUgkk=",
                  "content_base64sha512": "v+e5n7l7oySa8IzEPZv9ObIMby6XAQtZKEBtGVi0cB0vBTGH2lIsTIeN0FZ+eT2e1xBJBc2N89H2+6yeIMitkQ==",
                  "content_md5": "0b26e313ed4a7ca6904b0e9369e5b957",
                  "content_sha1": "91b7b0b1e27bfbf7bc646946f35fa972c47c2d32",
                  "content_sha256": "e2d0fe1585a63ec6009c8016ff8dda8b17719a637405a4e23c0ff81339148249",
                  "content_sha512": "bfe7b99fb97ba3249af08cc43d9bfd39b20c6f2e97010b5928406d1958b4701d2f053187da522c4c878dd0567e793d9ed7104905cd8df3d1f6fbac9e20c8ad91",
                  "directory_permission": "0777",
                  "file_permission": "0777",
                  "filename": "./testing",
                  "id": "91b7b0b1e27bfbf7bc646946f35fa972c47c2d32",
                  "sensitive_content": null,
                  "source": null
                },
                "sensitive_values": {
                  "sensitive_content": true
                }
              }
            ],
            "address": "module.file_creation"
          }
        ]
      }
    }
  },
  "configuration": {
    "provider_config": {
      "local": {
        "name": "local",
        "full_name": "registry.terraform.io/hashicorp/local"
      }
    },
    "root_module": {
      "resources": [
        {
          "address": "local_file.test",
          "mode": "managed",
          "type": "local_file",
          "name": "test",
          "provider_config_key": "local",
          "expressions": {
            "content": {
              "constant_value": "Cotent of the file"
            },
            "filename": {
              "references": [
                "var.filename"
              ]
            }
          },
          "schema_version": 0
        }
      ],
      "variables": {
        "filename": {
          "default": "./testing"
        }
      }
    }
  },
  "timestamp": "2023-12-02T16:29:39Z",
  "errored": false
}
```

### Understanding `apply`
- When we do a `terraform apply` Terraform created a plan and asked us to approve it before making any changes to your resources.
- When we approve the plan and apply this configuration, Terraform will:
	- **Lock your workspace's state, so that no other instances of Terraform will attempt to modify your state or apply changes to your resources**.
		- This is done by creating a `.terraform.tfstate.lock.info` file
		- If Terraform detects an *existing lock file (`.terraform.tfstate.lock.info`), it will report an error and exit*.
	- Create a plan, and wait for you to approve it. 
		- Alternatively, you can provide a *saved plan* created with the `terraform plan` command, in which case **Terraform will NOT prompt for approval**.
	- Execute the steps defined in the plan using the providers you installed when you initialized your configuration. 
		- **Terraform executes steps in parallel when possible**, and *sequentially when one resource depends on another*.
	- *Update your workspace's state with a snapshot of the new state of your resources*.
	- Unlock your workspace's state by deleting the lock file.
	- Report the changes it made, as well as any output values defined in your configuration.
- When Terraform **encounters an error** during an apply step, it will:
	- Log the error and report it to the console. 
	- *Update the state file with any changes to your resources*.
	- Unlock the state file and exit
- Your infrastructure may be in an invalid state after a Terraform apply step errors out.
	- **Terraform DOESNOT support automatically rolling back a partially-completed apply**. 
	- After you resolve the error, *you must apply your configuration again to update your infrastructure to the desired state*.

> [!note]- Changes to resources between the time you plan the changes and attempt to apply them can cause Terraform to error if the plan can no longer be applied.
> For example lets suppose we create a plan to do something with the S3 bucket and now before applying the plan if we delete the bucket manually then when we try to apply the plan it will error out since there is no S3 bucket. To fix this we would have to generate a new plan since the state of the resources has been changed.