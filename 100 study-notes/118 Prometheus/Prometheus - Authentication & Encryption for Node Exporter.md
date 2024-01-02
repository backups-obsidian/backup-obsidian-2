---
created: 2024-01-01 16:01
updated: 2024-01-01 18:39
---
---
**Links**: [[118 Prometheus Index]]

| Previous: [[Prometheus - Installation & Configuration]] |
|-|

| Next: [[Prometheus - Metrics & Labels]] |
|-|

---
## Authentication & Encryption
- *In case we DON'T have authentication anyone that has access to our target can scrape the metrics*.
- Encryption helps us to prevent data transfer in plain text.
- For encryption we need to generate a certificate key pair and specify it in the node exporter's settings.
	- ![[attachments/Pasted image 20240101181218.png]]
- We need to specify the certificate in prometheus' settings.
	- ![[attachments/Pasted image 20240101181301.png]]
- For authentication we need to first create a hashed password and specify it in the node exporter's settings.
	- ![[attachments/Pasted image 20240101181343.png]]
- We then modify the prometheus' settings to use basic auth.
	- ![[attachments/Pasted image 20240101181431.png]]