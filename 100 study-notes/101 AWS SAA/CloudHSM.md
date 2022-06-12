---
created: 2022-04-19 16:22
updated: 2022-05-04 16:19
---
---
**Links**: [[101 AWS SAA Index]]

---
- It stands for **Hardware Security Module**.
- In cloud HSM **AWS provides us with dedicated encryption hardware** and **we are responsible for managing our keys**.
- Cloud HSM *supports both symmetric and asymmetric encryption keys*.
- Good option to use **SSE-C type of encryption** since we get the hardware.
- CloudHSM is used for key management and storage but **not distribution**.
- CloudHSM *clusters are spread across Multi AZ* (HA) which is great for high availability and durability.
- We have **auditing which is independent of cloud trail**.
- Attempting to log in as the administrator more than twice with the wrong password zeroizes your HSM appliance. When an HSM is *zeroized, all keys, certificates, and other data on the HSM is destroyed*.

> [!caution] If you want a *managed service* for creating and controlling your encryption keys, but you *don't want or need to operate your own HSM*, consider using *AWS Key Management Service* over CloudHSM.