---
created: 2022-09-15 21:09
updated: 2022-10-01 20:08
---
---
**Links**: [[111 KodeCloud Index]]

---
## Network Policies
- Ingress and Egress and Traffic flow
	- ![[attachments/Pasted image 20221001185529.png]]
	- We can say the backend API server receives ingress traffic from the website and has egress traffic to database server (port 3306).
	- From the database server's perspective it receives ingress traffic from the API server.
	- ![[attachments/Pasted image 20221001185748.png]]

> [!important]+ *Response from the database server to the request of api server is not considered as an egress traffic*. 
> - When deciding the type of traffic (ingress or egress) you only need to be concerned about the direction in which the request originates.
> - Only when an outbound connection is initiated by the pod it is considered as egress. 
> - Once you allow inbound traffic the response to it is allowed automatically and it not considered as egress traffic.
> - *Since the database pod doesn't initiate any outbound connection, there is no egress traffic*.


- By default all the *pods can reach each other* using the **IPs or services or pod-names**.
	- k8s is configured with an **All Allow** rule by default which allows traffic from any pod to any other pod or services within the cluster.

- By default all the three pods can communicate with each other.
	- ![[attachments/Pasted image 20221001190134.png]]
- We would not want the frontend web server to communicate with the database pod directly.
	- This is where we implement network policies to allow traffic to the DB server only from the API server.

> [!note]- Network policy is another object in the k8s namespace just like pods, services etc.
> - We link a network policy to one or more pods and then define the rules within the network policy.
> ![[attachments/Pasted image 20221001190412.png]]
> ![[attachments/Pasted image 20221001190451.png]]

- We **use labels and selectors to link network policies to pods**.
- Sample network policy rule allowing traffic from the API pod to the database pod
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: db-policy
  namespace: prod
spec:
  # associating the pod (which we want to protect) to the network policy
  podSelector:
    matchLabels:
      role: db
  policyTypes:
  - Ingress
  ingress:
  - from:
    # pods from which ingress is allowed
    - podSelector:
        matchLabels:
          name: api-pod
    ports:
    - protocol: TCP
      port: 3306
```

- Listing the network policies:
	- `k get netpol`

- By default only the API pods in the prod namespace can access the database pod in the prod namespace.
	- API pods with the same labels will not be able to access the database pod unless the following is specified
	- ![[attachments/Pasted image 20221001192932.png]]

- If we don't have pod selector field in the definition file and just the namespace selector then all the pods in that namespace will be able to access the database pod.
	- ![[attachments/Pasted image 20221001193045.png]]

- Now suppose we have a backup selector somewhere outside of k8s. 
	- It won't be able to access the database pod since it is not a pod within the cluster. So the podSelector and namespaceSelector fields are useless.
	- *But we can configure the network policy to allow traffic originating from certain IP addresses*.
	- ![[attachments/Pasted image 20221001193414.png]]

> [!danger]- By default all pods allow ingress and egress traffic on all the ports. **But as soon as we apply a network policy everything gets blocked and we have to manually allow ingress and egress by specifying rules in the network policy definition file**.
> - If we only have ingress rules as shown in the example yaml file then we will only have ingress traffic and egress traffic will be blocked unless we specify egress rules.
> - The sample network policy will yield something like this when we describe it
> ![[attachments/Pasted image 20221001195930.png]]

- If the database pod pushes backup to an external server then we would need egress rules.

> [!caution]- Network policies are *enforced by the network solution implemented* on the k8s cluster and NOT all network solutions support network policies.
> ![[attachments/Pasted image 20221001191702.png]]
> With a network solution provider that does not support network policies you can still create network policies but they won't be enforced.

### Understanding rules
- These are the 3 (*podSelector, namespaceSelector, ipBlock*) main selectors in the ingress block, same applies for the egress block.
	- **Instead of `from` we have `to` in egress**.

- These can be passed as individual rules or together as a single rule.
- In this example we have 2 rules.
	- ![[attachments/Pasted image 20221001193741.png]]
	- **It works as an OR operation**. 
	- Traffic originating from either of the locations is allowed.
- *But within the first rule we have 2 selectors which means the traffic must pass both of these criteria to reach the database pod*. 
	- It works like an **AND operation**.
	
> [!caution]+ *Now if we separate the namespaceSelector as a separate rule then this would mean that database pod can get traffic from almost any pod*.
> - Since it can get traffic from all the pods in the prod namespace.
> - It can get traffic from all the api pods in other namespaces.
> ![[attachments/Pasted image 20221001194301.png]]

---

- Sample egress file with multiple destinations (there will be multiple `to` blocks):
	- ![[attachments/Pasted image 20221001200704.png]]