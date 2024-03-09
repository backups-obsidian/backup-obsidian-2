---
created: 2024-01-19 10:36
updated: 2024-01-20 10:25
---
---
**Links**: [[118 Prometheus Index]]

---
## Grafana
- While creating rules X is the time range to limit the data.

![[attachments/Pasted image 20240120102358.png]]

- During the evaluation period if only 1 point doesn't break the rule (during the `for` period) then the alert status is changed to normal and then the alert again has to go back to the start of the evaluation period (`for` period) if it is breached.