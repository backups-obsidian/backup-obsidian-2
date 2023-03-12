---
created: 2022-04-19 16:22
updated: 2023-03-11 08:42
---
---
**Links**: [[101 AWS SAA Index]]

---
## Services that will be used
### Data management and transfer
- **AWS Direct Connect**: Move GB/s of data to the cloud, over a *private secure network*
- **Snowball & Snowmobile**: Move *PB of data to the cloud*
- **AWS DataSync**: Move large amount of data between on-premise and S3, EFS, FSx for Windows

### Compute and Networking
- **Cluster placement group** for good performance (low latency, 10Gbps network)
- If you are **experiencing high packets per second (PPS)** or *require lower latencies* then it means you need *enhanced networking*.

- EC2 Enhanced Networking (**SR-IOV**) (**ENA**)
    - **Higher bandwidth**, **higher PPS** (packet per second), **lower latency**
        - Option I: **Elastic Network Adapter (ENA)** up to **100 Gbps**
	        - Works only for newer EC2 instances.
        - Option 2: *Intel* 82599 *VF* up to 10 Gbps *LEGACY*
		
-   Elastic Fabric Adapter (**EFA**)
    - Improved ENA for HPC, **only works for Linux**
    - Great for *inter-node communications*, tightly coupled workloads
    - **Leverages Message Passing Interface (MPI)** standard
    - **Bypasses the underlying Linux OS to provide low-latency, reliable transport**

> [!note] If you need *OS bypassing capabilities* use EFA.

> [!caution] If you have **windows instances** then you have to use **ENA** over EFA since EFA is supported by only Linux instances.

### Storage 
- **FSx for Lustre** is the best option.

### Automation and Orchestration
#### AWS Batch
- [[Other Services#Batch|AWS Batch]] supports **multi-node parallel jobs**, which enables you to run single jobs that span **multiple EC2 instances**.
- Easily *schedule jobs and launch EC2 instances accordingly*

#### AWS Parallel Cluster
- **Open source cluster management tool** to deploy HPC on AWS
- *Automate creation* of VPC, Subnet, cluster type and instance types