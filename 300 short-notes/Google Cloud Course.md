---
created: 2022-09-24 19:30
updated: 2022-09-26 23:57
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

### ACL
- ACLs define **who** has access to your buckets and objects, as well as **what level** of access they have 

> [!question]- How is ACL different from IAM?
> - IAM permissions apply to all objects within a bucket.
> - **ACLs can be used to customised specific accesses to different objects**.

- User gets access if he is allowed by either IAM or ACL!
- Use IAM for common permissions to all objects in a bucket
- Use ACLs if you need to customise access to individual objects
- Two types of access controls:
	- Uniform: Uniform bucket level access using IAM
		- Use Uniform access when all users have same level of access across all objects in a bucket
	- Fine-grained: Use IAM and ACLs to control access
		- Fine grained access with ACLs can be used when you need to customise the access at an object level.