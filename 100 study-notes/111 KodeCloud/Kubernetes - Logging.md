---
created: 2022-11-13 09:29
updated: 2022-11-13 17:56
---
---
**Links**: [[111 KodeCloud Index]]

---
## Metrics Logging
- **Prometheus** is the standard.
	- K8s exposes its metrics in the prometheus format.
- *Prometheus is pull based* so it pulls metrics from its targets.
- Prometheus stores the metrics in its internal time series database.
- Promql is prometheus's query language.

## Logs Logging
- Logs are distributed by nature. Every application has its own logs.
	- We need to collect them at a central place.
- It can be done using the **ELK** stack.
- **Loki** is another option. 
	- It is a database waiting for resources to *push* logs into it.
- We can also use **fluentd** for log aggregation.
	- For using *fluentd in k8s it is run as a daemonset* (1 pod per worker node).
	- It is just a database for logs so we need elastic search and kibana to view it hence the EFK stack.
- *Fluentbit is a light weight version of fluentd*.

## Dashboard
- **Grafana** is the de facto standard for *displaying logs and metrics*.
	- Grafana is used to get metrics from prometheus and logs from Loki.

---
![[attachments/Pasted image 20221113100042.png]]

## References
- [Understanding Logging: Containers & Microservices - YouTube](https://www.youtube.com/watch?v=MMVdkzeQ848) - **MUST WATCH**: For understanding logging in containers.
	- This is also important to understand why we should log to stdout.
- [Introduction to Fluentd: Collect logs and send almost anywhere - YouTube](https://www.youtube.com/watch?v=Gp0-7oVOtPw) - *Using fluentd (EFK) in docker compose*.
- [Fluentd on Kubernetes: Log collection explained - YouTube](https://www.youtube.com/watch?v=6kmHvXdAzIM) - *Using fluentd (EFK) in k8s*