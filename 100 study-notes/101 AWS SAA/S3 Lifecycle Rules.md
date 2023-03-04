---
created: 2022-04-19 16:22
updated: 2023-03-04 08:31
---
---
**Links**: [[101 AWS SAA Index]]

---
- We can transition between storage classes **but not each and every transition is possible**. Like from glacier we cannot go back to standard IA.
	- ![[attachments/Pasted image 20230304082543.png]]
	- You can only go down from a level and not up.
	- Remember *intelligent tiering is between Standard IA and One Zone IA*.

- Moving objects to different storage classes can be done manually or *automated* using *Lifecycle Rules*.

## Transition Action
- Defines when objects are transitioned from one storage class to another.

> [!caution]+ The lifecycle storage class transitions have a constraint when you want to transition from the STANDARD storage classes to either STANDARD_IA or ONEZONE_IA. The following *constraints* apply
> - For larger objects, there is a cost benefit for transitioning to STANDARD_IA or ONEZONE_IA. *Amazon S3 does not transition objects that are smaller than 128 KB* to the STANDARD_IA or ONEZONE_IA storage classes because it's not cost effective.
> - Objects must be **stored at least 30 days in the current storage class** before you can transition them to STANDARD_IA or ONEZONE_IA. For example, you cannot create a lifecycle rule to transition objects to the STANDARD_IA storage class one day after you create them. *Amazon S3 doesn't transition objects within the first 30 days because newer objects are often accessed more frequently or deleted sooner* than is suitable for STANDARD_IA or ONEZONE_IA storage.

> [!note] Data can be transitioned to *Glacier* **immediately** (0 days)

> [!question]- A rule in your company states that you should be able to recover your deleted S3 objects immediately for 30 days, although this may happen rarely. After this time, and for up to 365 days, deleted objects should be recoverable within 48 hours.
> - *Enable S3 Versioning* in order to have object versions, so that "deleted objects" are in fact hidden by a "delete marker" and can be recovered.
> - Transition the "non-current versions" of the object to *Standard IA*
> - Transition afterwards the "non-current versions" to *Glacier Deep Archive*


## Expiration Action
- Configure **objects to delete** **after some time**.
- **Use Cases** 
	- *log files after 365 days* 
	- *old versions (if versioning is enabled)*
	- *incomplete multi part uploads*
- Rules can be **created** for a **certain folder** (prefix). Ex: `s3://mybucket/mp3/*`

## S3 Analytics
-  We can setup S3 analytics to help determine when to transition objects **from Standard to Standard IA**. Same classes offered by Intelligent tiering.
- It **DOESNOT** work for **Glacier** or **One Zone IA**.
- *Report is updated daily*.

> [!tip] S3 Analytics can help in creating good lifecycle rules.