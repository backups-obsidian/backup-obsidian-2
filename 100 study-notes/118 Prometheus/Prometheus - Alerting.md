---
created: 2024-01-10 08:45
updated: 2024-01-16 10:51
---
---
**Links**: [[118 Prometheus Index]]

| Previous: [[Prometheus - Push Gateway]] |
|-|

---
## Alerting
- Prometheus allows us to define conditions, that if met trigger alerts.
	- *These conditions are defined as standard PromQL expressions*.
- **Prometheus decides to trigger an alert if any resulting vectors from the query will trigger an alert**.
	- ![[attachments/Pasted image 20240116083231.png | This will trigger an alert]]
- **If there are 2 results then prometheus will trigger 2 alerts**.
	- ![[attachments/Pasted image 20240116083317.png]]

> [!note]- Prometheus is *only responsible for triggering alerts*. It does not send notifications such as emails, text messages.
> - That responsibility is offloaded onto a separate process called Alertmanager.
> - One Alertmanager can support multiple Prometheus instances.
> 
> ![[attachments/Pasted image 20240116083426.png]]

- *Alerting rules are similar to recording rules* and can be placed right along side recording rules within a rule group.
	- ![[attachments/Pasted image 20240116083554.png]]
- The `for` clause tells Prometheus that an expression **must evaluate to true for a specified period of time before firing alert**.
	- This is important for removing false positives.
	- Take an example where you are checking if a node is up or not and there is no `for` duration then in that case even if the node is down for 1s and comes back up immediately an alert will be triggered which is unwanted.
	- Example:
		- ![[attachments/Pasted image 20240116083923.png | This example expects the node to be down for 5 minutes before firing an alert]]
		- Network issues could cause an individual scrape to fail, its best to specify a duration to avoid false alarms.
- We can see our alerts on the `Alerts` page in prometheus.
	- ![[attachments/Pasted image 20240116084129.png | If the alerts are not firing then they will be green]]
- Different stages of alerts:
	- **`Inactive`**: Expression has not returned any results.
	- **`Pending`**: *Expression returned results but it hasn't been long enough to be considered firing* (5m in the above example).
	- **`Firing`** - Active for more than the defined for clause (5m in the above example).
- Example of different stages of alert:
	- ![[attachments/Pasted image 20240116084441.png | Yellow means pending, node has been down for less than 3 minutes]]

### Labels & Annotations
- *Labels can be added to alerts* to provide a mechanism to **classify and match (for grouping alerts) specific alerts in Alertmanager**.
	- ![[attachments/Pasted image 20240116084619.png | Adding labels to alerts]]
	- ![[attachments/Pasted image 20240116084651.png | Labels got added to alerts in prometheus]]
- *Annotations can be used to provide additional information*, however **unlike labels they do no play a part in the alerts identity**. 
	- So they cannot be use for routing in Alertmanager.
- Annotations are *templated using Go templating language*.
	- To **access alert labels** use `{{.Labels}}`.
	- To *get instance label* use `{{.Labels.instance}}`.
	- To **get the firing sample value** use `{{.Value}}`.
- Annotations example:
	- ![[attachments/Pasted image 20240116084959.png | Including description as an annotation]]
	- ![[attachments/Pasted image 20240116085028.png]]

### Alertmanager Architecture
- Alertmanager is responsible for receiving alerts generated from Prometheus and converting them into notifications.
	- These notifications can include, pages, webhooks, email messages, and chat messages.
- Architecture diagram:
	- ![[attachments/Pasted image 20240116085334.png]]
- Architecture explanation:
	- Prometheus sends alerts to Alertmanager using the API. 
	- Once the alerts reach dispatcher they are grouped and sent to the inhibition block.
	- Inhibition groups allows us to suppress certain alerts if other alerts exists.
	- Silencing help us to mute alerts. For example if we are performing a maintenance on one of nodes we can suppress those alerts.
	- Routing section is responsible for figuring what alert is sent to who and how.
	- Notification engine is actually responsible for sending the alerts.

### Alertmanager Configuration
- Configuring prometheus to use Alertmanager by modifying `prometheus.yaml`:
	- ![[attachments/Pasted image 20240116085950.png]]
- **We use `alertmanager.yml` to configure the Alertmanager**.
	- ![[attachments/Pasted image 20240116101210.png]]
- *At the top level there is a fallback/default route*. Any alerts that don't match any of the other routes will match the default route.
	- ![[attachments/Pasted image 20240116101313.png | These alerts will be grouped by labels `alertname` and `job`. These alerts will go to the receiver `staff`]]
- *Every route has a list match statement and a receiver for alerts that match*.
	- ![[attachments/Pasted image 20240116101505.png | In this example, all alerts with the `job=Kubernetes` & `severity=ticket` labels will match this rule. These alerts that match will get sent to receiver `k8s-slack`]] 
- For matching routes we can either use `matchers` or `match_re`.
	- If the `match_re` keyword is used instead of `matchers`, then a list of regular expressions can be provided to match specific labels.
- We can configure *subroutes* (routes nested within routes) for further route matching.
	- ![[attachments/Pasted image 20240116101814.png | All alerts with label of `job=kubernetes` will be sent to receiver `k8s-email`. If the alert has a label `severity=pager` then it will be sent to `k8s-pager`. All other alerts with label `job=kubernetes` will match the main route and be sent to `k8s-email`]]
- As with Prometheus, *Alertmanager does not automatically pickup changes to its config file*. One of the following must be performed, for changes to take effect:
	- Restart Alertmanager
	- Send a SIGHUP
		- Sudo killall -HUP alertmanager
	- **НТТР Post to `/-/reload` endpoint**
- **By default, the first matching route wins**. 
	- So a specific alert would stop going down the route tree after the first match.
- But if we want an alert to match two routes then to continue processing down the tree we can add `continue: true`.
	- ![[attachments/Pasted image 20240116102328.png | In this example, we want all alerts to be sent to the receiver `alert-logs` and then if it has the label `jobs=Kubernetes` it should also match the following line and be sent to `k8s-email` receiver]]
- **By default Alertmanager will group all alerts for a route into a single group, which results in us receiving one big notification**.
- The `group_by` field allows us to specify a list of labels to group alerts by.
	- ![[attachments/Pasted image 20240116102550.png | A default route has its alerts grouped by the `team` label. The infra team has alerts grouped based on `region` and `env` labels. The child routes will inherit the grouping policy and group based on same 2 labels (region and env)]]

### Receivers & Notifiers
- *Receivers are responsible for taking grouped alerts and producing notifications*.
	- **Receivers contain various notifiers** (eg: slack, pageduty, email, etc), which are responsible for handling the actual notifications.
- Defining a slack receiver:
	- ![[attachments/Pasted image 20240116103001.png]]
- Certain notifier configs/settings will be the same across all receivers and can be moved to global config section.
	- ![[attachments/Pasted image 20240116103127.png]]
- **Messages from notifiers can be configured by making use of the Go templating system**.
	- ![[attachments/Pasted image 20240116103251.png | Available data for custom message]]
- *Examples of custom messages*:
	- ![[attachments/Pasted image 20240116103640.png]]
	- ![[attachments/Pasted image 20240116103707.png]]
	- ![[attachments/Pasted image 20240116103750.png]]
	- ![[attachments/Pasted image 20240116103833.png | Because we have 2 different groups (region and severity) the alerts are grouped into 2 different notifications]]
- 