---
created: 2024-01-01 19:12
updated: 2024-01-12 09:18
---
---
**Links**: [[118 Prometheus Index]]

| Previous: [[Prometheus - Metrics & Labels]] |
|-|

| Next: [[Prometheus - Application Instrumentation]] |
|-|

**Useful Links:**:
- [PromQL cheat sheet](https://promlabs.com/promql-cheat-sheet/)
- [Running PromQL queries for understanding on web](https://demo.promlens.com/)
- [Demo prometheus source](https://demo.promlens.com/)

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

- *For comparison operators the metric name is NOT dropped*.

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
	- `ignoring` keyword can be used to "ignore" on **labels** to ensure there is a match between 2 vectors.
		- ![[attachments/Pasted image 20240104084832.png]]
- `ignoring` keyword is used to *ignore a **list** of labels when matching*, the *`on` keyword is to specify **exact list** of labels to match on*.
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

## Functions
- Math functions: `ceil`, `floor`, `abs`:
	- ![[attachments/Pasted image 20240105090827.png]]
- Using the *`vector` function we can convert a scalar to a vector*:
	- Example: `vector(4)`
- Given a single element input vector, *`scalar` function returns the sample value of the single element as a scalar*. 
	- **If the input vector does not have exactly one element, it returns a NaN**.
		- ![[attachments/Pasted image 20240105091046.png]]
- We can sort the data using the `sort` function:
	- ![[attachments/Pasted image 20240105091124.png]]
- Metrics of `counter` type will just plot an increasing graph. *Instead we are more interested in the rate at which a `counter` metric increases*.
	- The `rate()` and `irate()` function provides the per second average rate of change.
- Understanding the `rate()` function:
	- ![[attachments/Pasted image 20240105091637.png | The red line represents a grouping of 1 minute intervals since we have specified 1m inside rate. In each grouping we take the first and the last element and then take the difference and divide it by the time interval.]]
- Understanding the `irate()` function:
	- ![[attachments/Pasted image 20240105091923.png | The main difference between `rate` and `irate` is that instead of using the first and last data point we use the last 2 data points in `irate`.]]

|                             rate                              |                                                   irate                                                    |
|:-------------------------------------------------------------:|:----------------------------------------------------------------------------------------------------------:|
|    Looks at the first and last data points within a range     |                             Looks at the last two data points within a ranged                              |
|         Effectively an *average rate over the range*          |                                              **Instant rate**                                              |
| Best used for **slow moving counters** and **alerting rules** | Should be used for **graphing volatile, fast-moving counters**. We would never want to use irate in alerts |
|                      Gives smooth graphs                      |                                             Gives spiky graphs                                             |

- Tips for using `rate`/`irate`:
	- Make sure there is **atleast 4 samples** within a given time range, ideally more.
		- So for a *15s scrape interval 60s window gives 4 samples*.
	- When combining rate with an aggregation operator, always take `rate()` first, then aggregate.
		- This is so rate function can detect counter resets.
		- Example query: `sum without(code, handler) (rate(http_requests_total[24h]))`
- *In general it is advisable to use `rate` over `irate` and `increase` for counters*.

### Subquery
- Maximum value over a 10m of a gauge metric: `max_over_time(node_filesystem_avail_bytes[10m])`
- We *CAN'T use this for `rate` since it returns an instant vector and not a range vector*.
	- We have to use subquery for it.
- Subquery format:
	- ![[attachments/Pasted image 20240105093210.png]]
	- ![[attachments/Pasted image 20240105093237.png]]

> [!note] It is basically used with `rate` and `irate` functions.

## Recording Rules
- Recording Rules allow Prometheus to *periodically evaluate PromQL expression and store the resulting times series generated by them*.
- Recording rules can be useful for:
	- Speeding up your dashboards.
	- Providing aggregated results for use elsewhere.
- We specify the recording rules in `rules.yml` file.
	- We specify the location of `rules.yml` in the `prometheus.yml` file.
	- ![[attachments/Pasted image 20240105095743.png]]
- Understanding the `rules.yml` file:
	- ![[attachments/Pasted image 20240105095901.png]]

> [!note] The *rules* inside the groups are run *sequentially* but the *groups* run in *parallel*.

- Recording rule example:
	- ![[attachments/Pasted image 20240105113725.png | Here `record` is the name of the rule and `example1` is the name of the group]]
	- So now when we put `node_memory_memFree_precent` in the expression browser it will give us the value of the expression.
		- ![[attachments/Pasted image 20240105113947.png]]
- Since rules run sequentially within a group, *one rule can refer another rule*.
	- ![[attachments/Pasted image 20240105114102.png]]
- Record rule naming best practices:
	- It should be of the format `level:metric_name:operations`.
	- `level` - indicates the aggregation level of the metric by the labels it has. This will always include the "job" label + any other relevant target labels.
	- `metric_name` - metric/time-series name.
	- `operations` - list of functions & aggregators that have been applied to the metric(i.e. sum/max/avg).
- Record rule naming example:
	- ![[attachments/Pasted image 20240105114410.png]]
- **It is a best practice to have all the rules for a specific job contained in a single group**.

## HTTP API
- Prometheus has an **HTTP API that can be used to execute queries** as well as gather information on alert, rules, and service-discovery related configs.
- This is useful for situations where using the built-in web gui isn't an option for example:
	- Building custom tools
	- 3rd party integrations
- We send a POST request to the API at `http://<prometheus-ip>/api/v1/query`.
	- Example:
		- ![[attachments/Pasted image 20240105114905.png]]