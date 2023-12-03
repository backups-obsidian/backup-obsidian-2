---
created: 2023-12-03 10:51
updated: 2023-12-03 10:52
---
---
**Links**: [[113 Terraform Index]]

---
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

