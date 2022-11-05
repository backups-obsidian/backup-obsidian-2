---
created: 2022-11-05 15:44
updated: 2022-11-05 16:09
---
---
**Links**: [[112 Nginx Index]]

---
## NGINX - Reverse Proxy
> [!note]- Nginx uses `proxy_pass` directive to forward the request to the upstream/backend server.

- Nginx does proxying by utilising the `proxy_pass` directive.
- A `proxy_pass` directive **moves the incoming request to the replacement destination **. 
	- The replacement destination can be an *IP address*, *domain name*, *upstream name (backend server)*, unix socket.

- Nginx's default behaviour is to close the connection before it initiates a new connection to the backend/upstream.
	- In this process some of the original request information is lost.
- To preserve header information we can use a directive called `proxy_set_header`.
	- The first argument is the header name and second argument is the value which should be populated in that header.
	- Nginx rewrites/redefines the headers before sending them to the backend.

```nginx.conf
http {
	server {
		listen 80;
		# these headers can also be added in the location context
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

		location / {
			# specifying the location of error and access logs.
			access_log /var/log/nginx/reverse-access.log;  
			error_log /var/log/nginx/reverse-error.log;
			proxy_pass http://10.1.1.4:9000/app1;
		}
	}
}
```

> [!important]- Reverse proxy sits in front of the web server.
> - It is possible that nginx is the reverse proxy sitting in front of nginx web server.
> - It is possible to have nginx behave as a webserver on `/` and behave as a reverse proxy on`/something` 

> [!caution]- CDNs are an advanced form of reverse proxy with most of the configuration and maintenance taken care of by a third-party.

### Localhost configuration example
```
server {
    # Port
    listen       8080;

    # Set the name of the virtual server
    server_name  _;

    # Proxy pass everything to http://localhost:4206/
    location / {
        proxy_pass http://localhost:4206;
        # Set the proxied host
        proxy_set_header Host localhost;
    }
}
```

### Sidecar proxy
- ![[attachments/Pasted image 20221105152535.png]]
- The proxy has a container port exposed on port 0.0.0.0:80 and is available to other pods. The application container has no container pods exposed and will only listen to requests on the loopback device on port 127.0.01:8080.

### Features
- **Load balancing**: Nginx load balance the client request to multiple upstream servers evenly which improve performance and provide redundancy in case of server failure. This helps to keep the application up all the time to serve client requests and provide better SLA for application.
- **Security**: Nginx server provides security to backend servers that exist in the private network by hiding their identity. The backend servers are unknown to the client that are making requests. it also provides a single point of access to multiple backend servers regardless of the backend network topology.
- **Caching**: Nginx can serve static content like image, videos, etc directly and deliver better performance. It reduces the load on the backend server by serving static content directly instead of forwarding it to the backend server for processing.
- **Logging**: *Nginx provides centralized logging for backed server request and response passing through it and provides a **single place** to audit and log for troubleshooting issues*.
- **TLS/SSL support**: Nginx allows secure communication between client and server using TLS/SSL connection. User data remains secure & encrypted while transferring over the wire using an HTTPS connection.
- **Protocol Support**: Nginx supports HTTP, HTTPS, HTTP/1.1, HTTP/2, gRPC - Hypertext Transport Protocol along with both IP4 & IP6 internet protocol.

### Common options
```
location/ {
	proxy_pass http://127.0.0.1:3000;
	proxy_http_version  1.1;
	proxy_cache_bypass  $http_upgrade;
	
	proxy_set_header Upgrade           $http_upgrade;
	proxy_set_header Connection        "upgrade";
	proxy_set_header Host              $host;
	proxy_set_header X-Real-IP         $remote_addr;
	proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
	proxy_set_header X-Forwarded-Proto $scheme;
	proxy_set_header X-Forwarded-Host  $host;
	proxy_set_header X-Forwarded-Port  $server_port;
	proxy_set_header X-Test            $request_uri;
}
```

-   `proxy_http_version 1.1` - Defines the HTTP protocol version for proxying, by default it it set to 1.0. For Websockets and `keepalive` connections you need to use the version 1.1.
-   `proxy_cache_bypass $http_upgrade` - Sets conditions under which the response will not be taken from a cache.
-   `Upgrade $http_upgrade` and `Connection "upgrade"` - These header fields are required if your application is using Websockets.
-   `Host $host` - The `$host` variable in the following order of precedence contains: hostname from the request line, or hostname from the `Host` request header field, or the server name matching a request.
-   `X-Real-IP $remote_addr` - Forwards the real visitor remote IP address to the proxied server.
-   `X-Forwarded-For $proxy_add_x_forwarded_for` - A list containing the IP addresses of every server the client has been proxied through.
-   `X-Forwarded-Proto $scheme` - When used inside an HTTPS server block, each HTTP response from the proxied server is rewritten to HTTPS.
-   `X-Forwarded-Host $host` - Defines the original host requested by the client.
-   `X-Forwarded-Port $server_port` - Defines the original port requested by the client.

### Handling Websockets
```nginx.conf
events {}
  
http {
    listen 80;
    server_name nginx-handbook.test

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
    }
}
```
- The `proxy_http_version` directive sets the HTTP version for the server. 
	- **By default it's 1.0, but web socket requires it to be at least 1.1**
- By writing `proxy_set_header Upgrade $http_upgrade;` you're instructing NGINX to pass the value of the `$http_upgrade` variable as a header named `Upgrade` – same for the `Connection` header.