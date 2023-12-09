---
created: 2023-12-03 10:51
updated: 2023-12-09 09:35
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Expressions]] |
|-|

| Next: [[Terraform - Miscellaneous]] |
|-|

---
## `count`
- The `count` argument **replicates the given resource or module** a specific number of times with an incrementing counter.
	- It is the easiest way to create multiple instances of the same resource.
	- It works best when resources will be identical.
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

## `for_each`
- **`for_each` works with a map or set of strings.
- If we have a map then then we access the key using `each.key` and value using `each.value`
	- In case we are using a set or list then `each.key` gives the index and `each.value` is the value of the item.

```hcl title:"Using for_each" fold
variable "explaining-map" {
	type  = map
	default = {
		"key1" = "value1"
		"key2" = "value2"
	}
}

resource "local_file" "testing" {
	for_each = var.explaining_map
	filename = each.key
	content = each.value
}
```