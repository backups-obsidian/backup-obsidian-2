---
created: 2024-01-01 18:15
updated: 2024-01-01 19:13
---
---
**Links**: [[118 Prometheus Index]]

| Previous: [[Prometheus - Authentication & Encryption for Node Exporter]] |
|-|

| Next: [[Prometheus - PromQL]] |
|-|

---
## Metrics
- Prometheus metrics have 3 different things:
	- Name
	- *Labels* - key value pairs
	- Value
- Syntax: **`<metric_name>[{<label_1="value_1">, <label_N="value_N">}] < metric_value>`**.
	- Example: `node_cpu_seconds_total{cpu="0",mode="idle"} 258277.86`.
		- Labels provide us information on which cpu this metric is for and for what cpu state(idle).
		- *If we have different CPUs then we will have the same metric with different labels*.
			- ![[attachments/Pasted image 20240101182055.png]]
- **Labels help us specify some more information about the metric**.
- When prometheus records the metrics it records the time stamp.

> [!important]+ Time series is a stream of time stamped values **sharing the same metric and set of labels**.
> ![[attachments/Pasted image 20240101182418.png]]
> In the above example we have **2 metrics** (`node_filesystem_files`, `node_cpu_seconds_total`) but a **total of 8 time series** (unique combination of metric and set of labels).

- *Rules for naming metrics*:
	- Metric name specifies a general feature of a system to be measured.
	- May contain ASClI letters, numbers, underscores, and colons.
	- **Must match the regex `[a-zA-Z_:][a-zA-ZO-9_:]*`**.
	- Colons are reserved only for recording rules

- **Every metric has a `TYPE` and a `HELP` attribute**.
	- `HELP` - *description of what the metric is*.
	- `TYPE` - *Specifies what type of metric* (counter, gauge, histogram, summary)

### Different type of metrics
#### Counter
- **How many times did X happen**.
- *Number can only increase*.
- Ideal for:
	- Total number of requests.
	- Total number of exceptions.
	- Total number of job executions.

#### Gauge
- **What is the current value of X**.
- *Can go up or down*.
- Ideal for:
	- *Current CPU Utilization*
	- *Available System Memory* 
	- Number of concurrent requests

#### Histogram
- **How long or how big something is**.
- *Groups observations into configurable bucket sizes*.
- Ideal for:
	- ![[attachments/Pasted image 20240101183332.png]]
	- The metric is cumulative which means that anything that is `< 0.5` will also include `< 0.3`

#### Summary
- Similar to histograms(track how long or how big).
- How many observations fell below X.
- Don't have to define quantiles ahead of time.
- Ideal for:
	- ![[attachments/Pasted image 20240101183610.png]]

## Labels
- Labels are *Key-Value pairs associated with a metric*.
- Allows us to **split up a metric by a specified criteria**.
- Metric *can have more than one label*.
- ASCIl lettrs, numbers, underscores.
- Must match regex `[a-zA-Z0-9_]*`.

> [!question]- Why use labels?
> - Imagine a scenario where we are building an API for an e-commerce application and have a metric for each of our endpoints.
> - It would be difficult to sum up all the metrics to get the total number of requests.
> - Instead we can have a single metric and specify the path as a label in the metric.
>
> ![[attachments/Pasted image 20240101184337.png]]

- In the above example if we wanted to track the HTTP methods of a particular endpoint then we could do this by adding another label.
	- ![[attachments/Pasted image 20240101184504.png]]

> [!important]- Metric name is just another label. So *everything is a label*.
> ![[attachments/Pasted image 20240101184635.png]]
> Labels surrounded by `__` are considered internal to prometheus.

- **Every metric is assigned 2 labels by default (`instance` and `job`)**.
	- ![[attachments/Pasted image 20240101184751.png]]