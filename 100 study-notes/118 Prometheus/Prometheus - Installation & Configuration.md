---
created: 2024-01-01 14:12
updated: 2024-01-01 18:10
---
---
**Links**: [[118 Prometheus Index]]

| Previous: [[Prometheus - Architecture]] |
|-|

| Next: [[Prometheus - Authentication & Encryption for Node Exporter]] |
|-|

---
## Downloading and Running Prometheus
- Download the tar file from the website and untar it.
- Inside the prometheus folder we will find the prometheus executable, promtool executable (used for verifying the prometheus configuration) and the `prometheus.yml` file for specifying the prometheus configuration.
	- ![[attachments/Pasted image 20240101141526.png]]
- We can run prometheus by running the executable using the `./prometheus` command.
- We can view the dashboard at `localhost:9090`.

## Running Prometheus as Systemd Service
- *Create a prometheus user for running prometheus*. 
	- Ensure that user CANNOT be used to login to the machine.
	- `sudo useradd --no-create-home --shell /bin/false prometheus`
- Create a folder `/etc/prometheus` for storing the configuration files and `/var/lib/prometheus` for storing the time series database.
	- Update the permissions so that these folders are owned by the `prometheus` user and `prometheus` group.
		- Example: `sudo chown -R prometheus:prometheus /etc/prometheus`
- Download and move the `prometheus` and `promtool` binary to `/usr/local/bin` folder.
	- Update the permissions of the binaries so that they are owned by the `prometheus` user and `prometheus` group.
- Copy the `consoles`, `consoles_libraries` folders and the `prometheus.yml` file to `/etc/prometheus`.
	- Update the permissions so that these files and folders are owned by the `prometheus` user and `prometheus` group.
- Command to start prometheus:

```bash title:"Command to start prometheus" fold
# -u is used to change to the prometheus user
sudo -u prometheus /usr/local/bin/prometheus \
	--config.file /etc/prometheus/prometheus.yml \
	--storage.tsdb.path /var/lib/prometheus/ \
	--web.console.templates=/etc/prometheus/consoles \
	--web.console.libraries=/etc/prometheus/console_libraries
```

- Create a service file at `/etc/systemd/system/prometheus.service`:
- Contents of the service file:

```bash title:"Service file" fold
[Unit]
Description=Prometheus
Wants=network-online.target # start after network is up
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
	--config.file /etc/prometheus/prometheus.yml \
	--storage.tsdb.path /var/lib/prometheus/ \
	--web.console.templates=/etc/prometheus/consoles \
	--web.console.libraries=/etc/prometheus/console_libraries
 
[Install]
WantedBy=multi-user.target # start service as a part of normal system start up
```

- Reload the daemon (`sudo systemctl daemon-reload`)
- Start the prometheus process (`sudo systemctl start prometheus`)
- Enable prometheus to start on boot (`sudo systemctl enable prometheus`)

> [!note]- We follow the same process as above to install and run the node exporter i.e. creating a systemd service and then starting it.
> On successful installation we should be able to get the metrics by running `curl localhost:9100/metrics`.

## Prometheus Configuration
- To retrieve the metrics from a specific target (for example node exporter) we have to configure prometheus using `prometheus.yml`.
- **Prometheus configuration example**:
	- ![[attachments/Pasted image 20240101154903.png]]
- Understanding `scrape_configs` section in the `promtheus.yml` file
	- ![[attachments/Pasted image 20240101155241.png]]
	- ![[attachments/Pasted image 20240101155257.png]]

> [!important] After we update the `prometheus.yml` file we need to restart prometheus.

```bash title:"Sample prometheus.yml file" fold
# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself. By default prometheus scrapes itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]
```