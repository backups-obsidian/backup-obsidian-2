---
created: 2024-01-01 18:54
updated: 2024-01-01 19:02
---
---
**Links**: [[118 Prometheus Index]]

---
## Promtools
- Promtools is a utility tool shipped with Prometheus that can be used to:
	- *Check and validate configuration*.
		- Validate `prometheus.yml`: 
			- `promtool check config /etc/prometheus/prometheus.yml`
		- Validate rules files
	- *Validate metrics passed to it are correctly formatted*.
	- Can perform queries on a Prometheus server.
	- Debugging & Profiling a Prometheus server.
	- Perform unit tests against Recording/Alerting rules.

- Using promtools Prometheus configs can be validated before applying them to a production server
	- This will prevent any unnecessary downtime while config issues are being identified.

## Getting Container Metrics
- **To get the metrics of a container we use `cAdvisor`**.
- It runs as a container and is responsible for exposing the container metrics.
	- ![[attachments/Pasted image 20240101190037.png]]

```yaml title:"Scrape config for using cAdvisor metrics" fold
scrape_configs:
  - job_name: "cAdvisor"
    static_configs:
    - targets: ["12.1.13.4:8080"]
```