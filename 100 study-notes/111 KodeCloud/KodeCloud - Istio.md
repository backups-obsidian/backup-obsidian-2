---
created: 2023-06-15 08:20
updated: 2023-06-15 15:53
---
---
**Links**: [[111 KodeCloud Index]]

---
## Service Mesh
- Service is a *dedicated* and *configurable infrastructure layer* that handles the communication between services *without having to change the code in a microservice architecture*.
	- ![[attachments/Pasted image 20230615082226.png]]
- **The proxies and the communication between them forms the Data Plane**.
- **The proxies talk to a server side component known as the Control Plane**.
- All the networking logic is abstracted from the business logic.
- Benefits of service mesh:
	- Dynamically configure how service meshes talk to each other.
	- *Mutual TLS*.
	- Observability.

## Istio
- *Istio implements the proxies using Envoy*.
- Istio architectural diagram:
	- ![[attachments/Pasted image 20230615083023.png]]
- Citadel is responsible for certificate generation.
- Pilot helps with service discovery.
- Galley helped in validating configuration files.
- *These 3 components were combined to a single daemon known as Istiod*.
- **Each pod has a separate component along with the envoy proxy known as istio agent**.
	- Istio agent is responsible for *passing configuration secrets* to the envoy proxy.
- Once Istio is installed in the cluster we *need to explicitly enable istio sidecar injection in the namespace where our apps are deployed.
	- ![[attachments/Pasted image 20230615084003.png]]

### Traffic Management
- When we installed istio we installed 3 components.
	- *Istiod*, *istio-ingressgateway* and *istio-egressgateway*.
- *Istio-ingressgateway is similar to an ingress controller like nginx*.
- *Istio deploys ingress gateways using envoy proxies*.
	- **So the ingress and egress gateways are just envoy proxies**.
- Example diagram:
	- ![[attachments/Pasted image 20230615091548.png]]
- Creating a gateway using a gateway object:
	- ![[attachments/Pasted image 20230615092458.png]]

### Virtual Services
- **Virtual services define a set of routing rules for traffic coming from the ingress gateway to the service mesh**.
- Virtual services also help in *weighted routing* for A/B testing.
	- ![[attachments/Pasted image 20230615094935.png]]
- Virtual services usage diagram:
	- ![[attachments/Pasted image 20230615151810.png]]

### Destination Rules
- We define subsets in the destination rules.
- We can have different load balancing for different subsets.
- We can *configure simple and mutual TLS using destination rules*.
- Diagram:
	- ![[attachments/Pasted image 20230615095855.png]]
- For hosts use fully qualified names instead of short names:
	- ![[attachments/Pasted image 20230615103055.png]]

---

> [!important] In Istio we *use virtual services instead of normal k8s services*.

### Authentication
- *JWT validation* & **mTLS**.
	- The request is actually coming from the expected service.
- **In mTLS each service gets its own identity which is enforced using certificate key pairs**.
- *Certificate generation and distribution is automatically managed by istiod*.