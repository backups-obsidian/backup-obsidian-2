---
created: 2024-01-01 19:12
updated: 2024-01-03 08:58
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