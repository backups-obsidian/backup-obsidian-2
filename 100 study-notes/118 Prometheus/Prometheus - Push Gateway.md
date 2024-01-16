---
created: 2024-01-10 08:30
updated: 2024-01-16 08:19
---
---
**Links**: [[118 Prometheus Index]]

| Previous: [[Prometheus - Service Discovery & Relabelling]] |
|-|

| Next: [[Prometheus - Alerting]] |
|-|

---
## Push Gateway
![[Prometheus - Architecture#Push Gateway]]

- We install push gateway by downloading its binary and running it as a service. 
	- By default it exposes its metrics at port 9091 on `/metrics`.
- Using push gateway is same as using any other target. 
	- Prometheus won't know that it is talking to a push gateway.

```yaml title:"prometheus.yaml for using push gateway as a target" fold
scrape_configs:
  - job_name: pushgateway
    honor_labels: true
    static_configs:
      - targets: ["192.168.1.168:9091"]
```

- In case of push gateway there will be multiple jobs sending metrics to it.
	- If prometheus scrapes metrics from the push gateway then it will automatically set the label to pushgateway for that metric which is not helpful.
	- *`honor_labels: true` allows metrics to specify custom values for the job label for a metric*.
- There are 2 different methods for sending metrics to the push gateway:
	- *Send HTTP Requests to Pushgateway*.
		- We need to send and HTTP POST request to the following URL `http://<pushgateway_address>: <port>/metrics/job/<job_name>/<label1>/<value1>/<label2>/<value2>`.
		- `<job_name>` will be the job label of the metrics pushed.
	- *Prometheus Client Libraries*