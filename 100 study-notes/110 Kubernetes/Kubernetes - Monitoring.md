---
created: 2022-08-29 17:44
updated: 2022-08-29 18:03
---
---
**Links**: [[110 Kubernetes Index]]

---
## Monitoring
- For **monitoring** a cluster we use **Prometheus**. It is a metrics aggregator. 
- **Grafana** is a **dash boarding tool** which uses *Prometheus as a data source*.
- Prometheus has become the main stream monitoring tool in container and micro service infrastructure. 
	- ![[attachments/Pasted image 20220829174508.png]]
- Prometheus offers automated monitoring and alerting.

- We specify which targets to monitor in the `prometheus.yml` file. 
	- By default it will have only one entry `localhost:9090`. This is the port prometheus is running on and prometheus monitors itself. 
	- Now we can add more targets with their `ip:port/metrics`.

### Prometheus
#### Prometheus Architecture
- Prometheus server is the core of prometheus and does the actual monitoring work. It is made up of 3 parts.
	- It has **time series database** which *stores the metrics data* like the CPU usage, no of exceptions etc.
	- It has a **data retrieval worker** which is *responsible for pulling these metrics* from the applications, servers etc. 
		- It then *pushes the data collected to the time series database*.
	- Last component is the **web server api** which *accepts PromQL queries for the stored data*. 
		- This is then used to create dashboards in Prometheus web UI or Grafana.

- Architecture diagram
	- ![[attachments/Pasted image 20220829174755.png]]

- Prometheus can monitor a lot of things. 
	- These things are known as **targets**. 
	- *Each target has units which is monitored*. The *unit of a target we want to monitor is known as metric*. 
	- The metrics are stored in Prometheus database component.

> [!question]- How does prometheus collect data from targets?
> - Prometheus **pulls metrics** from the targets from an *http endpoint* which by default is `hostaddress/metric`.
> - For it to work the **targets must expose this endpoint and data available must be in the format Prometheus understands**.
> ![[attachments/Pasted image 20220829175057.png]]

- *Prometheus data is stored in a custom time series format on disk*. 
	- So if you want to deploy it using docker then you will need persistent volumes. 
	- **The default retention time is 15d** after which it will start overwriting data. 
	- You can reduce it if you want to save space.

#### Exporters
- Some targets expose prometheus end points by default so there is no extra work to get metrics from them. 
- But many services don’t have Prometheus end point so an extra component is required.
- This component is known as **exporter**. 
- The exporter fetches the metrics from the target, exposes the end point and converts the metrics into a format which Prometheus understands.
	- ![[attachments/Pasted image 20220829175438.png]]
- Prometheus has a list of exporters like Mysql, elastic search, linux server etc.
	- *These exporters are also available as docker images*.
	- If you want to monitor a linux server then you can download the linux exporter.
	
> [!note]- So if you want to monitor you mysql pod in the k8s cluster then you can deploy a **side car container (Prometheus exporter)** which will connect to mysql container and export the metrics at `/metrics` endpoint.

#### Prometheus works on a pull mechanism
- It **pulls the metrics from the end point**. 
	- Most monitoring systems like AWS cloud watch are push systems. 
	- When a *lot of micro services push data it creates a lot of traffic*.
- Since we have a common endpoint data can be pulled by multiple Prometheus endpoints.
- If you have **small job which will run only for a specific duration (short-lived)** then it is advisable to use the *Prometheus pushgateway* so that these services can push data to Prometheus database.
	- ![[attachments/Pasted image 20220829175721.png]]

#### Prometheus Characteristics
- Prometheus is stand alone since it should work when other applications fail. 
- Since it is stand alone it is difficult to scale.
	- ![[attachments/Pasted image 20220829180027.png]]


### Grafana
- Grafana dashboards are difficult to create. 
- There is a market place where you can easily download these dashboards depending on the target. 
- For linux it is node exporter full.
- Simple Grafana architecture
	- ![[attachments/Pasted image 20220829180231.png]]