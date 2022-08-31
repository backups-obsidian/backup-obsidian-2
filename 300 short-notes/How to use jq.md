---
created: 2022-08-30 23:53
updated: 2022-08-30 23:59
---
---
**Links**: [[300 home]]

---
## Introduction
- Shells such as Bash can’t interpret and work with JSON directly.
- `jq` is a command line processor for JSON.
- `jq` is built around the **concept of filters** that work over a stream of JSON. 
	- Each filter *takes an input and emits JSON to standard out*. 
	- There are *many predefined filters* that we can use. 
	- And we can **effortlessly combine these filters using pipes** to quickly construct and apply complex operations and transformations to our JSON data.

## Prettify JSON
- `echo '{"fruit":{"name":"apple","color":"green","price":1.20}}' | jq '.'`
- `jq '.' fruit.json`
- Being able to prettify JSON is particularly useful when we want to retrieve data from an API and see the response in a clear, readable format.

## Accessing properties
- We can access property values by using another simple filter: the `.field` operator. 
- To find a property value, we simply *combine this filter followed by the property name*.
	- `jq '.fruit' fruit.json`
- We can also *chain property values* together, allowing us to access *nested objects*
	- `jq '.fruit.color' fruit.json`
- If we need to retrieve *multiple keys*, we can separate them using a **comma**
	- `jq '.fruit.color,.fruit.price' fruit.json`

> [!important]- If one of the properties has spaces or special characters, we need to wrap the property name in quotes when accessing it from the `jq` command.
> `echo '{ "with space": "hello" }' | jq '."with space"'`

## Arrays
- Iterating over an array
	- `jq '.[]'`
- Sample JSON
```json
[
  {
    name: apple,
    color: green,
    price: 1.2
  },
  {
    name: banana,
    color: yellow,
    price: 0.5
  },
  {
    name: kiwi,
    color: green,
    price: 1.25
  }
]
```
- Extract the name of each fruit from each object in the array
	- `jq '.[] | .name' fruits.json`
	- First, we iterate over the array using `.[]`. 
		- Then we can pass each object in the array to the next filter in the command using a pipe `|`. 
		- The last step is to output the name field from each object using `.name`
	- Slightly more concise version: `jq '.[].name' fruits.json`

- Accessing by index
	- `jq '.[1].price' fruits.json`