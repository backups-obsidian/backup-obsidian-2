---
created: 2022-09-24 19:30
updated: 2022-09-24 19:44
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
- General `gcloud` command syntax: `gcloud <GROUP> <SUBGROUP> ACTION `