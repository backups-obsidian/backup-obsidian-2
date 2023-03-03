---
created: 2022-05-16 12:39
updated: 2023-03-03 13:00
---
---
**Links**: [[102 AWS DVA Index]]

---
## Performance
- **RAM**:
	- From **128MB to 10GB** in 1 MB increments
	- The more RAM you add, the more CPU credits you get
	- At *1,792 MB*, a function has the equivalent of *one full vCPU*
	- *After 1,792 MB*, you get *more than one CPU*, and need to use *multi-threading in your code to benefit from it*.

> [!tip] If your application is CPU-bound (computation heavy), increase RAM

- **Timeout**:
	- **default 3 seconds** 
	- *maximum* is 900 seconds (**15 minutes**)

## Execution Context
- The execution context is a **temporary runtime environment** that *initialises any external dependencies of your lambda code*
- Great for database connections, HTTP clients, SDK clients, etc.
- The *execution context is maintained for some time* in anticipation of another Lambda function invocation
- The next function invocation can **re-use** the *context to execution time and save time in initialising connections objects*.
- **The execution context includes the `/tmp` directory which can be *shared* across invocations**.
	- This is great if you have to download file for each invocation.
	- If your Lambda function needs to *download a big file to work*
	- If your Lambda function needs *disk space to perform operations*
	- *Default size* is **512MB**
		- *Max size is 10GB*.
	- The *directory content remains when the execution context is frozen*, providing **transient cache** that can be used for multiple invocations (helpful to *checkpoint your work*)
	- For *permanent persistence* of object (non temporary), *use S3*

> [!important]- Execution context is *part of the code outside the function handler*
> - Try to initialise DB connections or *anything that takes time* outside the connection handler so that they can be reused across invocations.
> - This will **greatly improve function performance**
> ---
> ![[attachments/Pasted image 20220524113355.png]]

## Concurrency
- **Concurrency limit**: up to *1000* concurrent executions **for all the functions** in the per region.
	- Under sustained load, your function's concurrency bursts to an initial level between *500 and 3000 concurrent executions that varies per region*. 
	- After the initial burst, the function's capacity increases by an additional 500 concurrent executions each minute until either the load is accommodated, or the total concurrency of all functions in the region hits the limit.
	- *By default, AWS Lambda limits the total concurrent executions across all functions within a given region to 1000*. 
	- This **limit can be raised by requesting** for AWS to increase the limit of the concurrent executions of your account.

> [!note]- Concurrency calculations
> To calculate the concurrency requirements for the Lambda function simply multiply the **number of executions per second** (20) by the **time it takes to complete the execution** (20).
> Therefore, for this scenario, the calculation is 20 x 20 = 400.

- We can set our own *limit* to the number of concurrent executions known as **reserved concurrency** at the function level.
	- Each *invocation over the concurrency limit will trigger a Throttle*
- Throttle behaviour:
	- If **synchronous invocation** => return *ThrottleError 429*
	- If **asynchronous invocation** => *retry automatically and then go to DLQ*
- If you need a *higher limit, open a support ticket*

> [!question]- What happens if we don't set reserved concurrency (limit)  per function?
> - If we don't set reserved concurrency per function then there is a chance that one of the functions eats up the *region limit of 1000 concurrent executions*. 
> - This means other functions won't be able to scale and will throttle.
> ---
> ![[attachments/Pasted image 20220524115027.png]]

### Concurrency and asynchronous invocations
- If the function *doesn't have enough concurrency* available to process all events, *additional requests are throttled*.
- For *throttling errors (429)* and *system errors (500-series)*, Lambda **returns the event to the queue** and **attempts to run the function again for up to 6 hours**.
- The **retry interval increases exponentially** from 1 second after the first attempt to a maximum of 5 minutes.

### Cold Starts & Provisioned Concurrency
- **Cold Start**:
	- *New instance* - *code is loaded* and *code outside the handler run (init)*
	- If the *init is large* (code, dependencies, SDK, etc) this process can take some time.
	- *First request served by new instances has higher latency* than the rest
- **Provisioned Concurrency**:
	- *Concurrency is allocated* before the function is invoked (*in advance*)
	- So the *cold start never happens* and all invocations have **low latency**
	- **Application Auto Scaling** can manage concurrency (*schedule* or *target utilisation*)

## Limits per region
- **Execution**:
	- *Memory* allocation: *128MB - 10GB* (1 MB increments)
	- Maximum *execution time*: 900 seconds (*15 minutes*)
	- *Environment variables (4 KB)*
	- *Disk capacity* in the "function container" (in `/tmp`): *512 MB*
		- Can be increased to 10 GB.
	- *Concurrency executions: 1000* (can be increased)
- **Deployment**:
	- Lambda function deployment size (*compressed .zip*): *50 MB*
	- Size of *uncompressed deployment* (*code + dependencies*): *250 MB*
	- Can use the `/tmp` directory to load other files at startup
	- Size of *environment variables: 4 KB*

> [!note] If we have a *big file* then we should not use lambda. Limit of 512MB. 
