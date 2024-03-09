---
created: 2024-01-08 16:29
updated: 2024-01-21 09:53
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

## Relabelling
- Re-labeling - allows us to filter out Prometheus targets that we don't want to scrape and it allows relabel, drop labels that we don't need.
- Relabeling is *implemented as a series of transformation* steps that we can apply in different sections of the Prometheus configuration file to filter or modify a list of labeled objects.
- We can apply relabeling to the following types of labeled objects:
	- **Discovered scrape targets** (`relabel_configs` section in a `scrape_config` section).
	- **Individual samples from scrapes** (`metric_relabel_configs` section in a `scrape_config` section).
	- **Alerts sent to the Alertmanager** (`alert_relabel_configs` section in the `alertmanager_config` section).
	- **Samples written to remote storage systems** (`write_relabel_configs` section in the `remote_write` section).
- Example image:
	- ![[attachments/Pasted image 20240116082157.png]]
- A relabeling configuration section consists of a *list of rules* that are **applied in sequence to each labeled object**. Each rule in the chain may either modify or drop a labeled object:
	- ![[attachments/Pasted image 20240116082254.png | Once a relabeling step decides to drop a labeled object, no further relabeling steps are executed for this object and it is deleted from the output list (for example, for a target, this would mean it will not be scraped).]]

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

- Labels starting with a double underscore (`__`) are automatically **removed after the last relabeling rule** and thus do not make it into the final labels of an object.
	- These specially prefixed labels can be used for storing and passing around extra metadata during the relabeling phase to make relabeling decisions or changes to the object's labels. 
	- For example, a service discovery mechanism may initially supply additional metadata for each target in these "hidden" labels.

### `relabel_configs`
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

### `metric_relabel_configs`
- *`metric_relabel_configs` occurs after the scrape and has access to scraped metrics*.
- Configuration is identical to `relabel_configs`.
- **Example: drop metric** `http_errors_total`:
	- ![[attachments/Pasted image 20240109134706.png]]
	- We are using source label of `__name__` since `http_errors_total` is the name of the metric.
- **Example: rename a metric** from `http_errors_total` to `http_failures_total`:
	- ![[attachments/Pasted image 20240109134853.png]]
- **Example: dropping a label for a metric**:
	- ![[attachments/Pasted image 20240109134934.png]]

## Understanding the difference between `relabel_configs` and `metric_relabel_configs`
- https://www.robustperception.io/life-of-a-label/
- https://www.robustperception.io/relabel_configs-vs-metric_relabel_configs/