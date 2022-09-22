---
created: 2022-09-22 10:06
updated: 2022-09-22 16:56
---
---
**Links**: [[300 home]]

---
## IAM
- IAM answers the question of *who* can *do what* on *which resource*.

- *Permissions*: fine-grained access control on a resource
- *Roles*: a **collection of permissions** determining what a user can do
	- We have a few predefined roles but we can also create custom ones.
- *Members (or Identities)*: a unique ID associated with the account.
	- *Google accounts, service accounts, groups, cloud identity* (get identity even if you don't have a google account)
- *Policies*:  The Cloud IAM policy **binds one or more members to a role**. 
	- ![[attachments/Pasted image 20220922150742.png]]
	- When you want to define who (member) has what type of access (role) on a resource, you create a policy and attach it to the resource.
	- **Together member and role make up the policy which is then applied to the resource**.
- *Resources*: fundamental components of all Google Cloud services. 
	- IAM policies **can be attached to any of the resources in the resource hierarchy**.
		- This means it can be attached to an organisation, folder, project or an individual resource.
		- ![[attachments/Pasted image 20220922151038.png]]
- *Groups*: Help manage users at scale.

- Since projects are the logical grouping of resources they are the main attachment point for the policies.

> [!caution]+ Members, roles & policies
> ![[attachments/Pasted image 20220922120013.png]]

- *Service accounts are created to represent non human users*.

> [!question]- What would you do if want applications to act on resources on your behalf?
> - Create a service account for the application
> - Grant it access to google APIs
> - We can then grant groups of people access to this service account which can be easily revoked.

- Different types of roles:
	- *Primitive roles*: which include the *Owner, Editor, and Viewer roles* that **existed prior to the introduction of Cloud IAM**.
	- *Predefined roles*: which provide granular access for a specific service and **are managed by GCP**.
	- *Custom roles*: which provide *granular access according to a user-specified list of permissions*.
		- ![[attachments/Pasted image 20220922120832.png]]

> [!important]- You need to create keys for service accounts only if you want to use it from on- prem machines.
> If you are assigning service accounts to google resources (like compute) you don't need keys.

> [!caution]+ We have a *resource hierarchy*: **Organisation -> Folder -> Project -> VM/resource**
> - Suppose if have many buckets in our cloud storage.
> - If we want a service account to have to have admin access over all the buckets in the project then we give it a role of storage admin and then add it as a member to the project.
> - Now by the property of inheritance it will have admin access over all the buckets in the project.
> - If we want it to have admin access over only a particular bucket we can go to the permissions section of the bucket and add the service account roles there.
> - **So always apply the permissions at the lowest level. For example on the bucket**.
>
> ![[attachments/Pasted image 20220922144214.png]]
> - The *effective policy of a resource is the union of policies set on that resource and the policies inherited from the hierarchy*.
> - If you a viewer policy on a member at the resource level but they have editor access on the project level then the editor access will be applied to them.

- Best practices:
	- Avoid primitive roles.
	- Follow the principle of least privilege.

- Query evaluation:
	- Alice wants to delete bucket A
	- ![[attachments/Pasted image 20220922152049.png]]

- Conditions specify when policy bindings should apply.
	- ![[attachments/Pasted image 20220922154318.png]]

- **How to modify a policy**: Get-Modify-Set flow 
	- ![[attachments/Pasted image 20220922154516.png]]
	- Always submit the whole IAM policy.

- Creating a service account using CLI
	- `gcloud iam service-accounts create <name>`
	- For the above command to work we will also need `iam.serviceAccounts.create` permission.
	- A new service account will be created with no roles assigned to it.
- Get the IAM policy of all the members of the project:
	- `gcloud projects get-iam-policy <project-name>`
- List the roles associated with a service account in a project
```bash
gcloud projects get-iam-policy <YOUR GCLOUD PROJECT>  \
--flatten="bindings[].members" \
--format='table(bindings.role)' \
--filter="bindings.members:<YOUR SERVICE ACCOUNT>"
```

- Getting cloud storage iam-policy
	- `gsutil iam get gs://<bucket-name>`
	- **This won't list the inherited policies**. 
	- So if you have a policy at the project level it won't be listed here.

- Get and set iam-policy support is much better in SDK

## References
- [Google Cloud IAM - Strictly for associate exam!! - YouTube](https://www.youtube.com/watch?v=KRzdAD7DljE)
- [Service Accounts in Google Cloud - IAM in GCP. - YouTube](https://www.youtube.com/watch?v=qXuw--126Bk)
- [Chapter #8 - Cloud IAM Basics | identity & access management on google cloud platform (gcp) - YouTube](https://www.youtube.com/watch?v=PjBhIDOdLM0)
- [Advanced IAM: Hacks, tips, and tricks for policy management - YouTube](https://www.youtube.com/watch?v=X-kZ_eNtxyU)
	- How to programatically get and assign policies to resources
	- Use of etags for concurrency control.
- [Creating, managing, and retiring Service Accounts - YouTube](https://www.youtube.com/watch?v=2TdLmI3G5Rc)
- [google cloud platform - How do I list the roles associated with a gcp service account? - Stack Overflow](https://stackoverflow.com/questions/47006062/how-do-i-list-the-roles-associated-with-a-gcp-service-account)