---
created: 2022-08-29 21:02
updated: 2022-08-29 21:08
---
---
**Links**: [[110 Kubernetes Index]]

---
## Load Balancer
- If you want to **directly expose a service**, this is the default method. 
	- ![[attachments/Pasted image 20220829210218.png]]
- All traffic on the port you specify will be forwarded to the service. 
- **There is no filtering, no routing, etc**. 
- This means you can send almost any kind of traffic to it, like HTTP, TCP, UDP, Websockets, gRPC, or whatever.
- The big downside is that each service you expose with a LoadBalancer will get its own IP address, and you have to pay for a LoadBalancer per exposed service, which can get expensive!

## Ingress
- Ingress is *NOT a type of service*. 
- Instead, it sits in front of multiple services and act as a "**smart router**" or *entrypoint into your cluster*.
- You can do a lot of different things with an Ingress, and there are many types of Ingress controllers that have different capabilities.
- The default GKE (Google Kubernetes Engine) ingress controller will spin up a HTTP(S) Load Balancer for you. 
	- This will let you do both path based and subdomain based routing to backend services.
	- For example, you can send everything on foo.yourdomain.com to the foo service, and everything under the `yourdomain.com/bar/` path to the bar service.
- Ingress example
	- ![[attachments/Pasted image 20220829210524.png]]

### When should you use ingress?
- Ingress is probably the *most powerful way to expose your services, but can also be the most complicated*. 
- There are many types of Ingress controllers, from the [Google Cloud Load Balancer](https://cloud.google.com/kubernetes-engine/docs/tutorials/http-balancer), [Nginx](https://github.com/kubernetes/ingress-nginx), [Contour](https://github.com/heptio/contour), [Istio](https://istio.io/docs/tasks/traffic-management/ingress.html), and more. 
- There are also plugins for Ingress controllers, like the [cert-manager](https://github.com/jetstack/cert-manager), that can automatically provision SSL certificates for your services.
- **Ingress is the most useful if you want to _expose multiple services_ under the same IP address, and these services all use the same L7 protocol (typically HTTP)**. 
- **You only pay for one load balancer** if you are using the native GCP integration, and because Ingress is “smart” you can get a lot of features out of the box (like SSL, Auth, Routing, etc)

## References
- [Kubernetes NodePort vs LoadBalancer vs Ingress? When should I use what? | by Sandeep Dinesh | Google Cloud - Community | Medium](https://medium.com/google-cloud/kubernetes-nodeport-vs-loadbalancer-vs-ingress-when-should-i-use-what-922f010849e0)