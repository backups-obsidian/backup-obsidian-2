---
created: 2022-09-23 17:15
updated: 2023-05-08 14:11
---
---
**Links**: [[111 KodeCloud Index]]

---
## Ingress
- *Ingress* can be thought of as a *layer 7 load balancer* built into the k8s cluster and can be configured using definition files, just like any other object in k8s.

> [!note]- Even with *ingress we need to expose it* to make it accessible outside the cluster.
> - We can *publish it as a NodePort* or as a cloud native *LoadBalancer*.
> - But it is one time configuration, going forward we will be performing all the routing configuration, SSL etc on the ingress controller.

- The *solution we deploy* is known as the *ingress [[Kubernetes - Controllers#Controllers| controller]]* and the *configuration* we do is known as the *ingress resource*.
	- Ingress resources are also created using definition files.

- **A k8s cluster DOES NOT come with an ingress controller by default**.
	- So if you simply create ingress resources and expect them to work then they won't.

- An ingress controller is deployed just as an ordinary deployment.
- Once we have deployed the ingress controller, we need a service to expose it to the outside world.
	- This will link the service to the deployment.

> [!important]+ Ingress controllers have additional intelligence built into them to *monitor the k8s cluster for ingress resources*.
> - They configure the underlying ingress server when some changes are made to the ingress resources.
> - But for ingress to controller to do this it **requires a service account with right set of permissions**.
> - For this we create a service account with correct set of role and role bindings.

- There are 4 things required to setup the ingress controller: *deployment, service, service-account, configMap* (for configuration of the ingress server)
	- ![[attachments/Pasted image 20221001204039.png]]

- **An ingress resource is a set of rules and configurations applied on the ingress controller**.
	- An ingress resource is created with a k8s definition file.

- To get the ingress resources we do:
	- `k get ingress`
	- This will list only the ingress resources and not the ingress controller since ingress controller is a deployment.
	- If it is defined in another name space we can do: `k describe ingress/<ingress-name> -n <namespace>`

- Very simple ingress resource definition file
```yaml
apiVersion: extensions/vlbetal
kind: Ingress
metadata:
	name: ingress-wear
spec:
	backend:
		serviceName: wear-service
		servicePort: 80
```

- We use *rules (depending on domain names)* in ingress resource when we want to route traffic based on different conditions.
	- *Within each rule we can handle different paths*.
	- ![[attachments/Pasted image 20221001204800.png]]

> [!note]- We have rules at the top for each domain name and within each rule we have different paths.
> ![[attachments/Pasted image 20221001204959.png]]

- Ingress resource with multiple paths
	- ![[attachments/Pasted image 20221001205115.png]]

```yaml
apiversion: extensions/vibetal
kind: Ingress
metadata:
	name: ingress-wear-watch
spec:
  rules:
  - http:
      paths:
      - path: /wear
        backend:
          serviceName: wear-service
          servicePort: 80
      - path: /watch
        backend:
          serviceName: watch-service
          servicePort: 80
```

- We can describe the ingress resource using `k describe ingress/<ingress-name>`
- Default backend
	- If the user tries to access a URL that does not match any of these rules then the user is redirected to the service specified as the default-backed. In the example it is `default-http-backend`
		- ![[attachments/Pasted image 20221001205502.png]]
	- So we must remember to deploy such a service.
	- In this case it won't forward the request to any backend.
		- ![[attachments/Pasted image 20221002102914.png]]

- Ingress resource with 2 rules one path:
	- In the previous examples we didn't specify the `host` field. If we don't specify the host field it will consider it as `*`
	- ![[attachments/Pasted image 20221001205859.png]]

- Difference between 1 rule 2 paths and 2 rules 1 path:
	- ![[attachments/Pasted image 20221001205943.png]]

> [!danger]- Changes between the previous and current versions of ingress
> ![[attachments/Pasted image 20221001210202.png]]

- Ingress resources and ingress controllers can be deployed in different namespace.
	- *It is a best practice to deploy ingress resources belonging to different teams in their own namespace*.
	- No extra configuration is required from our end except from deploying it. The ingress controller will automatically associate the path to the specified service.

### Rewrite 
- Suppose we have the following ingress:
	- ![[attachments/Pasted image 20221002104751.png]]
	- `/pay` forwards the request to the `pay-service` which is in front of a deployment.
- Now this may not work since the application (pod) is expecting requests on `/` path but the ingress is sending it on `/pay`.
	- If we have logging we can verify this
	- ![[attachments/Pasted image 20221002104933.png]]
	- We get 404 errors for `/pay`
	- The requests are coming in as `/pay`
	- The ingress is forwarding the url as is and the application doesn't have a `/pay` path
		- ![[attachments/Pasted image 20221002105625.png]]
- For sending the request to `/` we need to have rewrite annotations.

### Another Rewrite example
- Our `watch` app displays the video streaming webpage at `http://<watch-service>:<port>/`
- Our `wear` app displays the apparel webpage at `http://<wear-service>:<port>/`
- We must configure Ingress to achieve the below. 
	- When user visits the URL, *his request should be forwarded internally to the URL on the right*. 

> [!caution]- **Note that the `/watch` and `/wear` URL path are what we configure on the ingress controller so we can forward users to the appropriate application in the backend**. The *applications don’t have this URL/Path configured on them*.
> - `http://<ingress-service>:<ingress-port>/watch` –> `http://<watch-service>:<port>/`
> - `http://<ingress-service>:<ingress-port>/wear` –> `http://<wear-service>:<port>/`

- Without the `rewrite-target` option, this is what would happen:
	- `http://<ingress-service>:<ingress-port>/watch` –> `http://<watch-service>:<port>/watch`
	- `http://<ingress-service>:<ingress-port>/wear` –> `http://<wear-service>:<port>/wear`

- **Notice `watch` and `wear` at the end of the target URLs**. 
	- The target applications are not configured with `/watch` or `/wear` paths. 
	- *They are different applications built specifically for their purpose, so they don’t expect `/watch` or `/wear` in the URLs*. 
	- And as *such the requests would fail and throw a `404` not found error*.

- To fix that we want to **ReWrite the URL** when the request is passed on to the watch or wear applications. 
	- We don’t want to pass in the same path that user typed in. So we specify the `rewrite-target` option. 
	- This rewrites the URL by replacing whatever is under `rules -> http -> paths -> path` which happens to be `/pay` in this case with the value in `rewrite-target`. This works just like a search and replace function.
		- For example: `replace(path, rewrite-target)`
		- In our case: `replace("/path","/")` 

- Sample yaml ingress file with rewriting 
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-ingress
  namespace: critical-space
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /pay
        backend:
          serviceName: pay-service
          servicePort: 8282
```


## References
- [Introduction - NGINX Ingress Controller (kubernetes.github.io)](https://kubernetes.github.io/ingress-nginx/examples/) - NGINX ingress controller documentation