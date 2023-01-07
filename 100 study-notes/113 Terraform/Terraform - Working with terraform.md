---
created: 2023-01-06 09:49
updated: 2023-01-07 11:07
---
---
**Links**: [[113 Terraform Index]]

---
## Mutable and Immutable Infrastructure
- We can upgrade a software on different machines using normal updates but there is a chance that some of the machines might not update because of some missing dependencies.
	- Over time all the servers will have different version of the software making it harder to debug.
	- This is known as *configuration drift*.
	- Example of *mutable infrastructure*.
- Instead when we want to update a software we install the new version on the fresh machine and then bring down the machines using the old version.
	- This is an example of *immutable infrastructure*.
	- **We cannot carry out in-place updates with immutable infrastructure**.
- Immutability makes it easier to version the infra and roll back.

### Lifecycle rules
- By default when terraform updates the resource it *treats the resource to be immutable* i.e. deletes the resource and then recreates a new one.
	- Like if we change the file permission then the *older file is first deleted and then a new file is created*.
		- ![[attachments/Pasted image 20230106100438.png]]
- This may not be a desirable behaviour in all cases. 
	- We *might want the resource to be created first before deleting a the old one* or we *may not want to delete the resource at all*.
	- The above behaviours can be achieved using life cycle rules.

> [!note]- Lifecycle rules are *applied when a resource is updated* and NOT created.

- `create_before_destroy`:
	- ![[attachments/Pasted image 20230106100756.png]]
	- This can lead to weird results. For example suppose that you already have a file created with name x. Now if we update something in the file, it will first try to create file with name x and then delete the same file since the file name doesn't change.
- `prevent_destroy`:
	- Terraform will *prevent any changes that might result in the resource getting destroyed*.
	- ![[attachments/Pasted image 20230106101040.png]]
	- This can prevent the resources from getting accidentally deleted like a database resource.
	- The resources can still be destroyed using the `terraform destroy` command.
	- This rule only applies to `terraform apply`

- `ignore_changes`:
	- This lifecycle rule will prevent a resource from being updated based on a list of attributes that we define within the lifecycle block.
	- In this example once the EC2 instance is created terraform will not force a change of it s tags. This means if we change the tag using some tool or manually, doing a `terraform apply` won't result in any changes. Without the `ignore_changes` flag terraform would have changed the tag back to "ProjectA-Webserver"
	- *Changes made to tags outside of terraform is completely ignored*.
	- ![[attachments/Pasted image 20230106101744.png]]
	- Since it is a list we can easily add more arguments.
	- If we want to ignore all arguments then we can add `ignore_changes = all`

- Summary of lifecycle rules
	- ![[attachments/Pasted image 20230106101901.png]]

## Datasources
- *Datasources allow terraform to read resources which are provisioned outside of terraform*.
- We define data resources using the `data` block. It is quite similar to `resource` block.
	- ![[attachments/Pasted image 20230106104830.png]]
	- Reading the data source and using it with other resource.
- *`data` block consists of specific arguments for a datasource*.
- **Data source only reads the infrastructure**.
- Difference between a data source and a resource:
	- ![[attachments/Pasted image 20230106105024.png]] 

## Meta Arguments
- Meta arguments can be used within any resource block to change the behaviour of resources.
	- Example of meta arguments are: `depends_on`, `lifecycle`

### Count
- Easiest way to create multiple instances of the same resource.
- Example
	- ![[attachments/Pasted image 20230107095947.png]]
	- The resources are identified with `pet[0], pet[1], pet[2]`
- The problem is since we have provided only one name it will create the same resource 3 times instead of creating 3 different resources.
- We need to have 3 different filenames and use them with count
	- ![[attachments/Pasted image 20230107100223.png]]
- Using `length` to get the size of the list
	- ![[attachments/Pasted image 20230107100338.png]]
- **Drawback of count**:
	- Suppose after creating 3 files using terraform and now we remove the the first file name from the filename variable. 
	- 2 files will be replaced and 1 will be deleted.
		- ![[attachments/Pasted image 20230107100827.png]]
	- When we *use count we are returned a list of resources* each identified by its index.
		- ![[attachments/Pasted image 20230107100945.png]]
	- The first element in the list is always 0, so when we deleted `/root/pets.txt` then the element `/root/dogs.txt` shifts up and takes its value and so on. 
		- ![[attachments/Pasted image 20230107101113.png]]
		- Terraform sees that resources at `pet[0]` and `pet[1]` have to be destroyed and replaced.

### for_each
- In count resources are created as a list. We can overcome this by using `for_each`
- **`for_each` only works with a map or a set**.
- Converting a list to a set
	- ![[attachments/Pasted image 20230107101532.png]]
	- Using `toset`
		- ![[attachments/Pasted image 20230107101621.png]]

> [!note]- *Resources are stored as a map and not a list*.
> Resources are no longer identified by index, they are identified by a key.

- Difference between the output of `count` and `for_each`:
	- ![[attachments/Pasted image 20230107101935.png]]

## Version Constraints
- Using a specific version of a provider
	- ![[attachments/Pasted image 20230107105041.png]]
	- It is inside the `terraform` block
- Some version constraints:
	- `version = "!= 2.0.0"`: Don't use version `2.0.0`
	- `version = "< 1.4.0"`
	- `version = "> 1.4.0"`
	- `version = "> 1.2.0, < 2.0.0, != 1.4.0"`: Mixing comparison operators
	- `version = "~> 1.2"`: Terraform can download the version 1.2 or any other incremental version. The major version cannot be changes this means it can go max upto 1.9.
		- If we use `version = "~> 1.2.0"` then it can only go upto `1.2.9`
	