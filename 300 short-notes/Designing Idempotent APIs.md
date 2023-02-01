---
created: 2023-01-30 21:04
updated: 2023-01-30 22:34
---
---
**Links**: [[300 home]]

---
## Designing Idempotent APIs
- An idempotent request is a **request that you can be retried multiple times without having side effects** in the backend.
	- For example GET is always an idempotent method. 
	- No matter how many GET calls you make it is not going to change anything.

### Why do we need idempotent APIs?
- We want to have resiliency in our APIs i.e. if something goes wrong it automatically recovers from failures.
- To achieve this we use different retry mechanisms like exponential backoff.

> [!question]- So what is the problem of retries for non idempotent APIs?
> - Potential problem of no response back to the client from the backend due to a network issue but request was sent to the database by the backend and an entry was created. 
> - We might end up with multiple entries with the same data incase of retries from the client.

- **The pre requisite for retries is an Idempotent API**.
	- ![[attachments/Pasted image 20230130221150.png]]

### Ways of creating idempotent APIs
- Passing the input arguments of the request through a hash function and storing the output in a database and then comparing the subsequent requests with the hash.
	- ![[attachments/Pasted image 20230130221341.png]]
	- One con of doing it this way is that if you wanted to create 2 resources of the same kind intentionally you wouldn't be able to do it.
- Another way of doing it is that the client generates a unique token and uses it for the request or any other subsequent retries.
	- We can mitigate the above con using this approach since we would be generating a new token for creating another resource.
	- One important thing is that we need to return syntactically identical response.
	- ![[attachments/Pasted image 20230130221943.png]]

## References
- [What is API Idempotency and Why Is It Important? - YouTube](https://www.youtube.com/watch?v=I08syTslan8) 
- [Designing Idempotent API Endpoints for Payments at Stripe - YouTube](https://www.youtube.com/watch?v=J2IcD9FZvZU)