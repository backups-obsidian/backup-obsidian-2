---
created: 2023-03-03 15:30
updated: 2023-03-10 09:17
---
---
**Links**: [[114 AWS SOA Index]]

---
## Volume Resizing
- We can **only increase** an EBS volume's **size** or **IOPS**.
	- We can also change the volume type like from gp2 to gp3.
- After resizing an EBS volume, **we need to repartition your drive**.
- After increasing the size, it's *possible for the volume to be in the  "optimisation" phase for a long time*. The volume is still usable.
	- There are 3 stages: *modifying, optimising, completed*.

> [!note]- We *CANNOT decrease* the size of an EBS volume.
> We can create a smaller volume and then *migrate* the data.

## Fast Snapshot Restore (FSR)
- EBS Snapshots stored in S3
- By *default*, there's a *latency of I/O operations the first time* each block is accessed since the **block must be pulled from S3**.
- *Solution*: 
	- Force the initialisation of the entire volume (using the dd or fio command), or 
	- **Enable FSR**
- FSR helps you to create a volume from a *snapshot that is fully initialised at creation* (no I/O latency).
- Enabled for a snapshot in a particular AZ (billed per minute - **very expensive**)
- Can be enabled on snapshots created by Data Lifecycle Manager.