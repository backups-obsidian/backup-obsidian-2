---
created: 2024-01-01 19:12
updated: 2024-01-04 09:31
---
---
**Links**: [[118 Prometheus Index]]

| Previous: [[Prometheus - Metrics & Labels]] |
|-|

| Next: [[Prometheus - PromQL]] |
|-|

---

## Introduction
- Short for *Prometheus Query Language*.
- **Main way to query metrics within Prometheus**.
- Data returned can be visualized in dashboards.
	- Grafana uses PromQL to query data to display in dasbhoards.
- Used to *build alerting rules* to notify administrators.
- A PromQL expression can *evaluate to one of four types*:
	- **String** - a simple string value (currently unused).
	- **Scalar** - a simple *numeric floating point value*.
	- **Instant Vector** - *set of time series* containing a single sample for each time series, *all sharing the same timestamp*.
		- Each time series means all the possible combinations of metrics and labels.
		- Example query:
			- ![[attachments/Pasted image 20240102084908.png]]
	- **Range Vector** - set of time series containing a range of data points over time for each time series.
		- Example query:
			- ![[attachments/Pasted image 20240102085020.png]]

## Selectors & Matchers
- If we run `node_filesystem_avail_bytes` it will return us an instant vector i.e. all the time series at a particular instant.
- **If we want to select a particular time series we use label matchers**.
- *When we use label selectors the value has to be in quotations*.
- There are 4 different types of label matchers
	- `=` - Exact match on a Label value.
		- Example query: `node_filesystem_avail_bytes{instance="node1"}`
			- ![[attachments/Pasted image 20240102085642.png]]
	- `!=` - Negative equality matcher - return time series that don't have the label.
		- Example query: `node_filesystem_avail_bytes{device!="tmpfs"}`
			- ![[attachments/Pasted image 20240102085755.png]]
	- `=~` - Regular expression matcher - matches time series with labels that match regex.
		- Example query to return all time series where device starts with "/dev/sda" (sda2 & sda3): `node_filesystem_avail_bytes{device=~"/dev/sda.*"}`
	- `!~` - Negative regular expression matcher.
		- Example query to return time series where mountpoint does not start with "/boot": `node_filesystem_avail_bytes{mountpoint!~"/boot.*"}`
- We can *combine multiple selectors by separating them with a comma*.
	- Example query to return all time series from node1 without a "device=tmpfs": `node_filesystem_avail_bytes{instance="node1", device!="tmpfs"}`
		- ![[attachments/Pasted image 20240102090139.png]]
- If we want a range vector then a sample query will look like this: `node_arp_entries{instance="node1"}[2m]`

## Offset Modifiers
- When we perform a *query it returns us the most recent value of that metric*.
- **If we want the value of a metric sometime in the past we use `offset` modifier**.
	- Example query returning the value 5 minutes in the past: `node_memory_Active_bytes {instance="node1"} offset 5m`
- Different time units:
	- ![[attachments/Pasted image 20240103083930.png]]
- Time units example:
	- ![[attachments/Pasted image 20240103084014.png]]
- **If we want to go back a specific point in time (unix timestamp) then we can use the `@` modifier**:
	- Example query: `node_memory_Active_bytes{instance="node1"} @1663265188`
		- ![[attachments/Pasted image 20240103084132.png]]
- We can combine `offset` and `@` modifier:
	- ![[attachments/Pasted image 20240103084248.png | The above example will give us the value of metric at *6:01*]]

> [!note]- The order does not matter when using the `offset` and `@` modifier together.
> ![[attachments/Pasted image 20240103085059.png]]

- The `offset` and `@` modifier also works with range vectors.
	- Example query: `node_memory_Active_bytes{instance="node1"}[2m] @1663265188 offset 10m`
		- ![[attachments/Pasted image 20240103085200.png]]

## Operators
### Arithmetic Operators
- Example query: `node_memory_Active_bytes{instance="node1"} + 10`.
- Example: 
	- ![[attachments/Pasted image 20240103092538.png | Converting bytes to kilobytes]]

> [!note] When using arithmetic operators the metric name is dropped

### Comparison Operators
- Example query to filter for values greater than 100: `node_network_flags › 100`.
- We use the *`bool` operator to find if the condition is true or false*.
	- **1 is true and 0 is false**.
	- Example query to find filesystems which have less that 1000 bytes available: `node_filesystem_avail_bytes < bool 1000`
		- ![[attachments/Pasted image 20240103092902.png]]
- *`bool` operators are mostly used for generating alerts*.

> [!important]- When an PromQL expression has multiple binary operators, they follow an order of precedence, from highest to lowest.
> Operators on the same precedence level are *left-associative*.

### Logical Operators
- Example query to return all time series greater than 1000 and less than 3000: `node_filesystem_avail_bytes > 1000 and node_filesystem_avail_bytes < 3000`
- `unless` operator *results in a vector consisting of elements on the left side* for which there are **NO elements on the right side**.
	- Example query: `node_filesystem_avail_bytes > 1000 unless node_filesystem_avail_bytes > 30000`
		- ![[attachments/Pasted image 20240104083930.png]]
	- It it much more intuitive to just use `<`.

## Vector Matching
- In all the above examples we were performing an operation of a vector with a scalar.
- In vector matching we use 2 vectors for an operation :
	- Example query: `node_filesystem_avail_bytes/node_filesystem_size_bytes * 100`
- In case of vector matching **samples with exactly the same labels get matched together**.
	- ![[attachments/Pasted image 20240104084412.png]]

> [!caution]- So for vector matching **all the labels must be the same** for vectors to match.
> ![[attachments/Pasted image 20240104084537.png]]

- There may be certain instances where an *operation needs to be performed on 2 vectors with differing labels*.
	- `ignoring` keyword can be used to "ignore" on labels to ensure there is a match between 2 vectors.
		- ![[attachments/Pasted image 20240104084832.png]]
- `ignoring` keyword is used to *ignore a label when matching*, the *`on` keyword is to specify **exact list** of labels to match on*.
	- ![[attachments/Pasted image 20240104084949.png]]
- Example:
	- ![[attachments/Pasted image 20240104085122.png]]
- **One-To-One vector matching**: *Every element in the vector on the left of the operator tries to find a single matching element on the right*.
	- ![[attachments/Pasted image 20240104085233.png]]
- **Many-To-One vector matching**: Each vector elements on the one side can match with multiple elements on the many side.
	- Example error:
		- ![[attachments/Pasted image 20240104085436.png]]
	- Example solution:
		- ![[attachments/Pasted image 20240104085519.png]]
		- ![[attachments/Pasted image 20240104085554.png]]

## Aggregation Operators
- Aggregation operators, allow you to *take an instant vector and aggregate its elements, resulting in a new instant vector, with fewer elements*.
- Example:
	- ![[attachments/Pasted image 20240104090221.png]]
- **We can choose labels to aggregate on using the `by` label**.
	- ![[attachments/Pasted image 20240104090313.png]]
	- ![[attachments/Pasted image 20240104090344.png]]
- We can *pass in more than 1 label to group things together*:
	- ![[attachments/Pasted image 20240104093009.png]]
- **The `without` keyword does the opposite of `by` and tells the query which labels not to include in the aggregation**.
	- ![[attachments/Pasted image 20240104093116.png]]

