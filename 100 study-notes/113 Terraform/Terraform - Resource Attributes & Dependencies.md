---
created: 2023-01-02 21:55
updated: 2023-12-03 18:02
---
---
**Links**: [[113 Terraform Index]]

| Previous: [[Terraform - Variables]] |
|-|

| Next: [[Terraform - State]] |
|-|

---
## Resource Attributes
```hcl title:"example of using resource attributes" fold
resource "local_file" "pet" {
	filename = var.filename
	content = "My favorite pet is ${random_pet.my-pet.id}"
}

resource "random_pet" "my-pet" {
	prefix = var.prefix
	separator = var.separator
	length = var.length
}
```

- In the above example I am using `id` returned by the `random_pet` resource.
	- Every resource might have 0 or more resource attributes.
	- `random_pet` resource has one resource attribute called `id`

## Resource Dependencies
- In the above example due to the dependency terraform knows to create `random_pet` resource first and then create the `local_file` resource. 
- **When resources are deleted they are deleted in the reverse order**. 
	- `local_file` first and then the `random_pet`
- This type of dependency is known as the **implicit dependency**. 
	- We are not specifying which resources are dependent on what, terraform figures it out itself.
 - We can specify **dependency explicitly using `depends_on`** argument.
	 - This is necessary *only when a resource relies on some other resource indirectly* and it *does not make use of a reference expression*.

```hcl title:"example of explicit resource dependency" fold
resource "local file" "pet" {
	filename = var.filename
	content = "My favorite pet is Mr.Cat"
	depends_on = [
		random_pet.my-pet
	]
}

resource "random pet" "my-pet" {
	prefix = var.prefix
	separator = var.separator
	length = var.length
}
```

> [!note] The order in which resources are declared in the configuration files has no effect on the order in which Terraform creates or destroys them.