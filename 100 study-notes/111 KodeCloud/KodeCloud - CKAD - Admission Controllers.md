---
created: 2022-09-15 21:09
updated: 2023-05-15 07:56
---
---
**Links**: 
- [[111 KodeCloud Index]]
- [[Kubernetes - Steps of an API Request in API Server | Steps of an API Request in API Server]]

---
## Why Admission Controllers
- RBAC is good for enforcing basic rules but sometimes we want *more granular control* or *some extra checks*.
	- Like reviewing the configuration file and saying that only certain images are permitted from certain registry.
	- Or *enforce that we should never use the latest tag for any images*.
	- Or *do not permit run as root user*. 
	- Or enforce that metadata always contains labels.
- *These things CAN'T be achieved using authorization and this is where admission controllers are used*.
- Admission controllers offer a **highly extensible solution for performing security checks that go above and beyond the authentication, authorization, and network security rules** you enforce through other types of Kubernetes policy frameworks.
- They also play a central role in connecting Kubernetes to external security tools that can deliver even more advanced analytics and compliance features.
- Admission Controller enables DevOps and Security personnel to enforce deployment requirements and restrictions in the cluster upon every workload start and any configuration change.

## Admission Controllers
- An admission controller **intercepts** and processes requests to the Kubernetes API **prior to persistence of the object** in etcd, but **after the request is authenticated and authorized**.
	- ![[attachments/Pasted image 20230511144725.png]]
- Admission controllers have different functions but implement a typical two-phase admission control process.
	- The **first phase** is called the _change of request object_ or **mutating phase** (using *[[KodeCloud - CKAD - Admission Controllers#^aae81b | Mutating Admission Controller]]*). 
		- In this phase, changes are made to the incoming request.
	- This is followed by the allow/disallow of requests—the **second** or **validating phase** (using *[[KodeCloud - CKAD - Admission Controllers#^4b28f2 | Validating Admission Controller]]*) which acts on changes made in the mutating phase. 
		- If any request is disallowed in the validating phase, the entire request gets rejected, and error messages are returned as responses.

> [!note] Mutating admission controllers are followed by validating admission controller since any changes made to the object by the mutating admission controller can then be validated by the validating admission controller.

### Types of Admission Controllers
- Admission controllers are divided into two basic classes for ease of use.
	- **Mutating admission controllers:** These admission controllers assess the API object and may add or modify the API object contents. ^aae81b
	- **Validating admission controllers:** These types of admission controllers validate API object contents and make sure they are valid.  ^4b28f2
		- The responses are returned as binary results in the form of `yes`, if the object is granted permission, or `no` when it is not.

> [!important]- **Admission controllers can be both validating and mutating controllers**. 
> - A great example of that is the *LimitRanger admission controller*, which can handle functioning for both of these controller methods. 
> - As a validating admission controller, it can allow or deny the deployment objects from violating the limit range constraints. 
> - Meanwhile, as a mutating admission controller, it can change or assign resource limits for pods.

## Static (built-in) Admission Controllers
- *Static admission controllers are the built-in admission controllers that come with k8s*.
- Kubernetes recommends enabling several built-in admission controllers by default to secure the running containers.
- Some static admission controllers are: `AlwaysPullImages`, `NamespaceLifecycle`, etc.
- Command to view the list of static admission controllers:
	- `k exec <name-of-apiserver-pod> -n kube-system -- kube-apiserver -h | grep enable-admission-plugins`

## Dynamic Admission Controllers
- In Kubernetes, **dynamic admission controllers are user-configured controllers** that can *modify or reject API requests* based on custom logic.
- These controllers can extend the Kubernetes API functionality and *provide customized configuration through* **webhooks**.

### Dynamic Admission Controller Types
- There are **three specific types of dynamic admission controllers** that can extend API functionality through webhooks:
	- `ImagePolicyWebhook`: Controls admission for a particular container image.
	- `MutatingAdmissionWebhook`: Modifies the objects received in the admission requests. It *runs before the validating webhooks*.
	- `ValidatingAdmissionWebhook`: Accepts or rejects admission requests.
- To create and configure a webhook in the Kubernetes cluster, `MutatingAdmissionWebhook` and `ValidatingAdmissionWebhook` must be enabled.

### Example of a dynamic admission controller
- *Open Policy Agent (OPA)* is an open-source framework used to enforce policy decisions on clusters, CI/CD pipelines, and microservices.
 - Kubernetes specific implementation of OPA happens through an *OPA Gatekeeper agent*, which **acts as a validating webhook** to enforce the policies. 
	 - Gatekeeper **only checks whether the request meets the defined criteria or not**, which is why it is *installed as a validating webhook*.
- Another example of a dynamic validating webhook is *kyverno*.

## External Policy Engines (Kyverno vs OPA Gatekeeper)
- There are two leading open source policy engines for Kubernetes: 
	- Open Policy Agent (OPA) Gatekeeper 
	- Kyverno.

- *Kyverno’s* primary advantage is that it *DOESN'T require learning an additional language*. 
	- All of its policies are defined as Kubernetes resources. 
- In contrast, *Gatekeeper leverages OPA’s declarative language, Rego*. 
- Gatekeeper is part of the larger OPA system, while Kyverno is a standalone project for Kubernetes. 
- Gatekeeper is the more mature project, but *Kyverno features a smaller learning curve*.

## References
- [What Are Kubernetes Admission Controllers And Why They Matter (loft.sh)](https://loft.sh/blog/kubernetes-admission-controllers-what-they-are-and-why-they-matter/)