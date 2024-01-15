---
created: 2024-01-01 13:25
updated: 2024-01-01 15:53
---
---
**Links**: [[118 Prometheus Index]]

| Previous: [[Prometheus - Introduction]] |
|-|

| Next: [[Prometheus - Installation & Configuration]] |
|-|

---
## Architecture
- Simple architecture diagram:
	- ![[attachments/Pasted image 20240101131052.png]]
- Full architecture diagram:
	- ![[attachments/Pasted image 20240101133356.png]]

### Components
#### Retrieval, TSDB & HTTP Server
- The retrieval component is responsible for scraping the data.
	- Prometheus collects metrics by sending **HTTP GET requests to `/metrics` endpoint of each target**.
	- It is possible to configure the endpoint if we don't want to use `/metrics`.
- The metrics are then stored in a time series database.
- There is an HTTP server that accepts PromQL query so that the user can query the time series database.

#### Exporters
- Most systems by default DON'T collect metrics and expose them on an HTTP endpoint to be consumed by a Prometheus server.
- **Exporters** are *processes that run on the target* and are responsible for serving the metrics so that prometheus can pull the metrics.
	- ![[attachments/Pasted image 20240101134256.png]]
- Prometheus has *several native exporters*:
	- **Node exporters(Linux servers)**
	- Windows
	- MySQL
	- Apache
	- HAProxy 

#### Push Gateway
- Prometheus is a **pull based architecture** which means it pulls the metrics from the targets.
	- Some other pull based monitoring solutions are Zabix and Nagios.
- In case there are *short lived jobs* where the job doesn't last long enough for prometheus to scrape it we use the **Push Gateway**.
	- The short lived job sends the metrics to the push gateway and then *prometheus can query the push gateway like any other target*.

#### Service Discovery
- Since Prometheus is a *pull based monitoring* solution it *requires us to hard code all the targets that it needs to scrape*.
	- We can specify the targets in a configuration file.
- **Service discovery** in prometheus *automatically updates the list of targets to scrape* in a dynamic environments (Kubernetes).
- In a push based monitoring solution the targets will have to be configured to send metric data to the metrics server. 
	- There would be no need of service discovery.
	- Some push based systems are: Logstash, Garphite, OpenTSDB.

#### Alert Manager
- **Prometheus can be used to generate alerts** but it is *NOT responsible for sending the notifications*.
- Alert Manager handles all the notifications like SMS, Emails, Slack messages etc.
- So when an alert is triggered it is sent to Alert Manager.

#### Grafana or Prometheus Web UI
- We can use Grafana or Prometheus web UI to query the PromQL server.

### Monitoring Application Metrics
- If we want to monitor application metrics like:
	- Number of errors/exceptions
	- Latency of requests
	- Job execution time
- Prometheus comes with client libraries that allow us to expose any application metrics we need Prometheus to track.

### Pull vs Push based systems
- Some of the *benefits* of using a pull based system:
	- **Easier to tell if a target is down**.
		- In a pushed based system we DON'T know if its down or has been decommissioned
	- Push based systems could potentially overload metrics server if too many incoming connections get flooded at same time.
	- Pull based systems have a **definitive list of targets to monitor, creating a central source of truth**.
- *Push based monitoring excels* in the following areas:
	- *Event-based systems*, pulling data WOULDN'T be a viable option
		- However, Prometheus is for collecting metrics and not monitoring events
	- *Short lived jobs*, as they may end before a pull can occur
		- Prometheus has feature called Push Gateway to handle this situation.