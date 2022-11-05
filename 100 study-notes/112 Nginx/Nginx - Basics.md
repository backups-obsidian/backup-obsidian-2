---
created: 2022-11-05 16:13
updated: 2022-11-05 16:15
---
---
**Links**: [[112 Nginx Index]]

---
## NGINX Basics
> [!note]- Location of nginx configuration files in docker: `/etc/nginx`
> Configuration file: `nginx.conf`

- **The key value pairs in nginx are known as directives**.
	- *Directives must end with semicolon*.
	- ![[attachments/Pasted image 20221103201502.png]]
- **The blocks of code in the curly braces is known as context**.
	- Within the context we can have directives particular to that context.

- When we change nginx configuration we need to *RELOAD* it.
	- `nginx -s reload`
- Validating NGINX configuration files.
	- `nginx -t`

### Variables
```nginx.conf
events {
}

http {
    server {
        listen 80;
        server_name nginx-handbook.test;
        return 200 "Host - $host\nURI - $uri\nArgs - $args\n";
    }
}

```

```shell
# output
# curl http://nginx-handbook.test/user?name=Farhan

# Host - nginx-handbook.test
# URI - /user
# Args - name=Farhan
```

### Logging
- By default, NGINX's log files are located inside `/var/log/nginx`
- Any request to the server is logged by the `acces.log` file.
- We can have different access file names for different contexts.
- Example: 
```nginx.conf
events {}
http {
    server {
        listen 80;
        server_name nginx-handbook.test;
        
        location / {
            return 200 "this will be logged to the default file.\n";
        }
        
        location = /admin {
            access_log /var/logs/nginx/admin.log;
            return 200 "this will be logged in a separate file.\n";
        }
        
        location = /no_logging {
            access_log off;
            return 200 "this will not be logged.\n";
        }
    }
}
```

```shell
curl http://nginx-handbook.test/no_logging
# this will not be logged

sudo cat /var/log/nginx/access.log
# empty

curl http://nginx-handbook.test/admin
# this will be logged in a separate file.

sudo cat /var/log/nginx/access.log
# empty

sudo cat /var/log/nginx/admin.log 
# 192.168.20.20 - - [25/Apr/2021:11:13:53 +0000] "GET /admin HTTP/1.1" 200 40 "-" "curl/7.68.0"

curl  http://nginx-handbook.test/
# this will be logged to the default file.

sudo cat /var/log/nginx/access.log 
# 192.168.20.20 - - [25/Apr/2021:11:15:14 +0000] "GET / HTTP/1.1" 200 41 "-" "curl/7.68.0"
```

- The `error.log` file holds the failure logs. 
	- To make an entry to the error.log, you'll have to make NGINX crash.

> [!note]- For some reason if you are not seeing logs use `nginx -s reopen`

### Nginx Performance
- A rule of thumb in determining the optimal number of worker processes is **number of worker process = number of CPU cores**.
	- Getting the number of CPU cores in linux `nproc`

- NGINX provides a better way to deal with this issue. 
	- You can simply set the number of worker processes to `auto` and NGINX will set the number of processes based on the number of CPUs automatically.
	-`worker_processes auto;`

- Worker connection indicates the highest number of connections a single worker process can handle.
	- This can be found using `ulimit -n`
```nginx.conf
events {
    worker_connections 1024;
}
```