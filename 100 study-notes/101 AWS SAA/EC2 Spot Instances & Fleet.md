---
created: 2022-04-19 19:08
updated: 2023-03-10 08:07
---
---
**Links**: [[101 AWS SAA Index]]

---
## Spot Request
- To get a spot instance we create a **Spot Request**.
- The spot request can be **one time** or **persistent**.
- A spot request has the following required parameters defined by us → `max price`, `no of instances`, `request type`, `valid from to valid until(can be infinite)`, `launch specification (ami)`

### The difference between one time and persistent request
-   **One Time request** → once the request is *fulfilled* instances are launched and the *spot request will go away*.
-   **Persistent** → we want x number of instances from the spot request *as long as the spot request is valid* (`valid from → valid until`). 
	- If the instances are **interrupted** because current spot price > max price then the spot request will go back into action and *will restart* the instances when spot price < max price.
	- If you **stop** the instances then the *instances will not be started automatically*.

> [!info] Cancelling a spot request does not terminate the instances. So it is our responsibility to terminate the running instances.

> [!question]- How to terminate instances in a persistent request?
> To terminate all the spot instances in a persistent request you must *first cancel the spot request* and *then terminate the instances*. Since in a persistent request *if you terminate the spot instances first* then the *feedback loop* will see that x number of instances were needed but there are 0 now so it *will start them again*.
> ---
> ![[attachments/Pasted image 20230302094916.png]]

## Spot Fleets
- Set of **spot instances** + optional **on demand instances**.
- We define **launch pools**.
- Spot fleet *stops* when *maximum capacity* is reached *or* *maximum cost* defined by us is reached.
- By default, Spot Fleets are set to maintain target capacity by launching replacement instances after Spot Instances in the fleet are terminated.
- *Strategies* to launch spot instances: 
	- **lowestPrice**: *short workloads* 
	- **diversified**: *long workloads*
	- **capacityOptimised**: optimal capacity
 
> [!note] Spot fleet allows us to **automatically request instances with the lowest price** if we use the **lowestPrice** strategy.

- The main *difference* between a *spot request* and a *spot fleet* is that in a spot request *we know exactly the type of instance* we want whereas in spot fleet *handles the selection of instance* based on the strategy.

## Spot Block (Deprecated)
- Might still be asked in exam.
- Spot blocks are designed **not to be interrupted** and will run continuously for the duration *you select* (**1 to 6 hours**), *independent of the Spot market price*.
- Only in rare situations, spot blocks may be interrupted due to Amazon EC2 capacity needs.