---
created: 2023-03-03 15:17
updated: 2023-03-03 15:18
---
---
**Links**: [[114 AWS SOA Index]]

---
## CloudWatch Metrics
- Invocations - number of times your function is invoked (success/failure)
- Duration - amount of time your function spends processing an event
- Errors - number of invocations that result in a function error
- Throttles - number of invocation requests that are throttled (no concurrency available)
- *DeadLetterErrors* - number of times Lambda failed to send an event to a DLQ (*async invocations*)
- **IteratorAge** - time between when a Stream receives a record and when the Event Source Mapping sends the event to the function (for Event Source Mapping that reads from Stream)
- ConcurrentExecutions - number of function instances that are processing events

> [!note]- We can *build alarms* on top of these metrics.
> For example if throttles > 0 then our lambdas are getting throttled or if the errors > 0 then there are function errors.

- We can use CloudWatch insights to search through all the CloudWatch logs.

## Lambda Insights
- *Collects*, *aggregates*, and summarises:
	- *System-level Metrics* - CPU time, memory, disk, network
	- *Diagnostic Information* - cold starts, Lambda worker shutdowns
- It will be collected from the lambda and will be sent to **lambda-insights log group**.
- We must the CloudWatch Lambda extension for insights **as a lambda layer**.