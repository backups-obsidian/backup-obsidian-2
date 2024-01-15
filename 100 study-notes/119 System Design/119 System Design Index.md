---
created: 2024-01-13 16:45
updated: 2024-01-13 19:10
---
---
**Links**: [[../100 home|100 home]]

---

[[System Design - CAP Theorem]]
[[System Design - Consistent Hashing]]

- When presented with an *ambiguous system design question* first ask what are the **functional requirements**.
	- For example if the interviewer asks you to design YouTube then ask what feature (search, recommendation, liking, upload, watch etc) should be designed.
- Some **non functional requirements** to think about is 
	- *Reliability*
	- *Scale*
	- *Availability*
	- *Latency*
- Think if the system is **read heavy or write heavy**.
	- In case of a read heavy system add read replicas.
	- If we want to make database reads faster then we can add an in memory cache (redis).
- Depending on the requirement we will have to *choose between availability and consistency (CAP theorem)*.
- Use load balancer for scaling and reliability.
- Use object storage like S3 for storing large files.
- Use queues for asynchronous processing.
- Use CDNs for reducing user latency.
- Choose between TCP or UDP depending on the requirements.
- Apply rate limits to APIs.
- Prefer NoSQL databases over SQL databases if you want to scale the data.
	- SQL databases can also be scaled using sharding but there should be some router that decides in which shard the data lives.
- *Estimate the data you are dealing with by doing some math*.
	- For example if you are building a storage service and the application will have a million uploads a day and each upload is about 5 MB then the application needs to process 5 Million bytes per day.
	- *If not provided then make some assumptions and check with the interviewer*.
- Always start with a very simple design and then think about scalability later.
- Identify single point of failures in the system and discuss what can be done to fix it.
- Always justify why you are doing a particular thing.
- **DONT JUMP INTO THE SOLUTION WITHOUT ASKING QUESTION**.
- Define the APIs for the functional requirements like the name and the parameters they will take and what will be the response.