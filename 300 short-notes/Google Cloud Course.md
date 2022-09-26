---
created: 2022-09-24 19:30
updated: 2022-09-25 23:35
---
---
**Links**: [[300 home]]

---
## Misc
- Each project is assigned a globally unique ID.

## `gcloud`
- `glcoud` is the command line utility to interact with resources.
- It can be used to to manage almost all google cloud resources.
- Some GCP services have specific tools like 
	- `gsutil`: storage
	- `bq`: big query
	- `kubectl`: k8s clusters
- `gcloud` is a part of google cloud SDK
- We can see the version of the CLI tools that were installed with the gcloud SDK using: `gcloud --version`
- Initialise or reinitialise gcloud: `gcloud init`
	- This will ask if you want to reinitialise the existing configuration or create a new one.
	- The user id/service account, project and region that we want to use.
- List all the configs:
	- `gcloud config list`

## IAM
- Service accounts don't use passwords, they use private/public RSA key pairs.
- Service account types:
	- *Default Service Account*: **Automatically created when some services are used**. 
		- (NOT RECOMMENDED) since it has Editor role by default
	- *User Managed*: User created
		- (RECOMMENDED) *Provides fine grained access control*.
	- *Google-managed service accounts*: Created and managed by Google
		- Used by GCP to perform operations on user's behalf
		- In general, we DO NOT need to worry about them

- We will be mostly making use of default and user managed service accounts
- When service accounts are attached to cloud resources (like VMs) then *Key generation and use are automatically handled by IAM when we assign a service account to the instance*.
	- Keys are automatically rotated 
	- No need to store credentials in config files

- Service accounts and different use case scenarios
	- ![[attachments/Pasted image 20220925164351.png]]
	- ![[attachments/Pasted image 20220925164423.png]]
	- ![[attachments/Pasted image 20220925164322.png]]

