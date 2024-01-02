---
created: 2024-01-01 12:41
updated: 2024-01-01 13:26
---
---
**Links**: [[118 Prometheus Index]]

| Previous: [[Prometheus - Observability Basics]] |
|-|

| Next: [[Prometheus - Architecture]] |
|-|

---
## Introduction
### Use Cases
- Collecting metrics from different servers and creating a dashboard out of it.
- Several outages have occurred due to high memory on the server hosting a MySql database. 
	- Operations team would like to be notified through email when the memory gets to 80% max capacity so that proactive measure can be taken
- A new video upload feature has been added to the website, however there is some concerns about users uploading too large of videos. 
	- *The team would like to find out at which video length the applications starts to degrade and to do this, they need a chart plotting the average file size of upload and the average latency per request*.

### Basics
- Prometheus is an *open-source* monitoring tool that collects metrics data, and provide tools to visualize the collected data.
- In addition, Prometheus allows us to generate alerts when metrics reach a user specified threshold.
- **Prometheus collects metrics by scraping targets who expose metrics through an HTTP endpoint**.
- **Scraped metrics are then stored in a time series database which can be queried using Prometheus' built-in query language PromQL**.
- So what kind of metrics can Prometheus Monitor?
	- CPU/Memory Utilization
	- Disk space
	- Service Uptime
	- Application specific data
		- Number of exceptions
		- Latency
		- Pending Requests
- It has a lot of integration with softwares, platforms and databases that we commonly use.

> [!note]- Prometheus is designed to monitor *time-series data* that is **numeric**.
> What type of data should *Prometheus not monitor*: Events, System logs, Traces.

