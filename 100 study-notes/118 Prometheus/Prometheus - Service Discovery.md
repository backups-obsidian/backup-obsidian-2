---
created: 2024-01-08 16:29
updated: 2024-01-10 08:30
---
---
**Links**: [[118 Prometheus Index]]

| Previous: [[Prometheus - Application Instrumentation]] |
|-|

| Next: [[Prometheus - Push Gateway]] |
|-|

---
## Service Discovery
![[Prometheus - Architecture#Service Discovery]]

- Service Discovery allows Prometheus to *populate a list of endpoints to scrape* that *can get dynamically updated as new endpoints get created & destroyed*.
- Prometheus has *built-in support* for several service discovery mechanisms like EC2, Azure, GCE, Consul, Nomad, *Kubernetes*.
- A simple example of file service discovery where the endpoints are listed in a JSON file:
	- ![[attachments/Pasted image 20240109083956.png]]
- EC2 Service Discovery makes it so Prometheus has a list of all available EC2 instances to scrape.

```yaml title:"prometheus.yaml file for ec2 service discovery" fold
# scrape_configs can contain both static and dynamic configs (service discovery)
scrape_configs:
  - job_name: EC2
    ec2_sd_configs: # sd stands for service discovery
      - region: <region>
        access_key: <access key>
        secret_key: <secret key>
```

- Credentials should be setup with an IAM user with the `AmazonEC2ReadOnlyAccess` policy.

### Relabelling
- Re-labeling - allows us to filter out Prometheus targets that we don't want to scrape and it allows relabel, drop labels that we don't need.

```yaml title:"prometheus.yaml file for relabelling" fold
# scrape_configs can contain both static and dynamic configs (service discovery)
scrape_configs:
  - job_name: EC2
    relabel_configs: # relabelling occurs before scrape occurs and only has access to labels added by service discovery
    metric_relabel_configs: # relabelling occurs after the scrape
    ec2_sd_configs: # sd stands for service discovery
      - region: <region>
        access_key: <access key>
        secret_key: <secret key>
```

#### `relabel_configs`
- `relabel_configs` example:
	- ![[attachments/Pasted image 20240109085151.png]]
- **Dropping targets based on labels** (`action: drop`):
	- ![[attachments/Pasted image 20240109085230.png]]
- Using multiple source labels:
	- ![[attachments/Pasted image 20240109085321.png]]
- **Target labels are labels that are added to the labels of every time series returned from a scrape**.
	- We will always have `instance` and `job` as the target labels.
		- ![[attachments/Pasted image 20240109085447.png]]
- *Changing the value of a source label in the target label* (`action: replace`):
	- ![[attachments/Pasted image 20240109085626.png]]
- Dropping labels (`action: labeldrop`):
	- *By default all labels starting with `__` are dropped i.e. why we only have `instance` and `job` in the target labels*.
		- ![[attachments/Pasted image 20240109133923.png]]
	- ![[attachments/Pasted image 20240109085805.png]]
- Keeping labels (`action: labelkeep`):
	- **It keeps the specified labels and drops all other labels**.
	- ![[attachments/Pasted image 20240109133847.png]]
- Renaming the `__` source labels to keep them in the target label (`action: labelmap`).
	- **Unlike the `replace` action, the `labelmap` replacement property modifies the label name not the value**.
	- ![[attachments/Pasted image 20240109134351.png]]
	- ![[attachments/Pasted image 20240109134422.png]]

> [!caution]- The action `labeldrop` just drops the labels from the target labels whereas the action `drop` drops the target itself.
> - *`labeldrop` also DOESN'T require `source_labels`*.
> - For example if we do a `labeldrop` on regex `size` then there would be no labels of `size` in the target labels.
> - Whereas if we do a `drop` on a source label size with some regex then it will drop all those targets which have size equal to the value specified in regex.

#### `metric_relabel_configs`
- *`metric_relabel_configs` occurs after the scrape and has access to scraped metrics*.
- Configuration is identical to `relabel_configs`.
- **Example: drop metric** `http_errors_total`:
	- ![[attachments/Pasted image 20240109134706.png]]
	- We are using source label of `__name__` since `http_errors_total` is the name of the metric.
- **Example: rename a metric** from `http_errors_total` to `http_failures_total`:
	- ![[attachments/Pasted image 20240109134853.png]]
- **Example: dropping a label for a metric**:
	- ![[attachments/Pasted image 20240109134934.png]]