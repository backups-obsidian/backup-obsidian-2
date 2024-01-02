---
created: 2024-01-01 12:06
updated: 2024-01-01 12:55
---
---
**Links**: [[118 Prometheus Index]]

| Next: [[Prometheus - Introduction]] |
|-|

---
## Pillars of Observability
- There are *3 pillars of observability*

### Logs
- *Logs can be difficult to use to debug an issue* due to the verbosity of the logs outputted by systems/applications.
- Logs of processes are likely to be interwoven with other concurrent processes spread across multiple systems

### Traces
- Traces allows us to follow operations as they traverses through various systems & services.
	- So we can *follow an individual request* and see it flow through our system hop by hop.
	- Traces help us connect the dots on how processes and services work together.
- **Each trace has a trace-id** that can be used to identify a request as it traverses the system.
- **Individual events forming a trace are called spans**.
- Each span tracks the following:
	- *Start time*
	- *Duration*
	- *Parent-id*
- Example trace diagram:
	- ![[attachments/Pasted image 20240101121211.png]]
 
### Metrics
- Metrics provide information about the **state of a system using numerical values**. 
- Some examples are:
	- CPU Load
	- Number of open files
	- HTTP response times
	- Number of errors
- The data collected can be *aggregated over time* and graphed using visualization tools to *identify trends over time*.
- Metrics contain **4 pieces of information**:
	- Metric name
	- Value - most recent or current value of the metric
	- Timestamp for the metric
	- *Dimensions - additional information about the metric*
- Example of 4 pieces of information
	- ![[attachments/Pasted image 20240101122413.png]]

> [!note] Prometheus is a monitoring solution that is responsible for collecting and aggregating **metrics**.

## SLI/SLO/SLA
### SLI
- **Service Level Indicator(SLI)** is a *quantitative measure* of some aspect of the *level of service that is provided*.
- Common SLIs:
	- Request Latency
	- Error Rate
	- Saturation
	- Throughput
	- Availability

> [!important]+ Not all metrics make for good SLIs. We want to *find metrics that accurately measure a user's experience*.
> Things like **high-cpu/high-memory make for a poor SLI** as a user might not see any impact on their end during these events.

### SLO
- **Service Level Object(SLO)** is a *target value or range for an SLI* that we want to shoot for.
- For example if 
	- *SLI - Latency* then *SLO - Latency < 100ms*.
	- *SLI - availability* then *SLO - 99.9% uptime*.
- **SLOs should be directly related to the customer experience**. 
	- The main purpose of the SLO is to quantify reliability of a product to a customer.
- For SLOs it maybe *tempting to set them to aggressive values* like 100% uptime however this will come at a higher cost.
	- **The goal is NOT to achieve perfection but instead to make customers happy with the right level of reliability**.
	- If a customer is *happy with 99% reliability increasing it any further doesn't add any other value*.

### SLA
- **Service Level Agreement(SLA)** is just a *contract between a vendor and a user that guarantees a certain SLO*.
- The consequences for NOT meeting any SLO can be financial based but can also be variety of other things as well.
