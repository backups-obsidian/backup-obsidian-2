---
created: 2022-11-05 15:38
updated: 2022-11-05 15:44
---
---
**Links**: [[112 Nginx Index]]

---
- Nginx acting as a load balancer
	- ![[attachments/Pasted image 20221103200509.png]]
- Perform TLS termination

- In the **`upstream` (backend servers) context** we specify the backend servers.

```nginx.conf
http {
	upstream backend {
		server 127.0.0.1:1111;
		server 127.0.0.1:2222;
	}
	server {
		listen 80;
		location / {
			proxy_pass http://backendserver/;
		}
	}
}
```