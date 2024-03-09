---
created: 2024-01-01 18:54
updated: 2024-01-16 16:18
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

## Monitoring Kubernetes
- For monitoring kubernetes we can use the `kube-prometheus-stack` helm chart.
- We need to monitor the following for monitoring a kubernetes cluster:
	- Control-Plane Components (api-server, coredns, kube-scheduler)
	- Kubelet (cAdvisor) - exposing container metrics
	- `Kube-state-metrics` - cluster level metrics(deployments, pod metrics)
	- Node-exporter - Run on all nodes for host related metrics(cpu, mem, network)
- **Lets suppose we have deployed an application that exports prometheus metrics. We would want prometheus to scrape it**. There are 2 different ways of doing it.
	- Adding information in the `additionalScrapeConfigs` section in the `values.yaml`.
	- *Updating the service monitor CRD*.
- **Service monitors define a set of targets for prometheus to monitor and scrape**.
	- They allow us to avoid touching prometheus configs directly and give us a declarative Kubernetes syntax to define targets.
- Example:
	- ![[attachments/Pasted image 20240116161852.png]]