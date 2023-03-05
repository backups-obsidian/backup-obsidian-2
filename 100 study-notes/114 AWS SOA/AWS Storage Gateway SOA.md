---
created: 2023-03-05 16:44
updated: 2023-03-05 16:44
---
---
**Links**: [[114 AWS SOA Index]]

---
## Miscellaneous
- **File Gateway is POSIX compliant** (Linux file system)
	- POSIX metadata ownership, permissions, and timestamps stored in the object's metadata in S3
- *Reboot Storage Gateway VM*: (e.g. maintenance)
	- **File Gateway**: simply *restart* the Storage Gateway VM
	- **Volume and Tape Gateway**:
		- *Stop* Storage Gateway Service (AWS Console, VM local Console, Storage Gateway API)
		- *Reboot* the Storage Gateway VM
		- *Start* Storage Gateway Service (AWS Console, VM local Console, Storage Gateway API)

## Activations
- Two ways to get Activation Key:
	- Using the **Gateway VM CLI**
	- Make a **web request to the Gateway** VM (*Port 80*)
- *Troubleshooting* Activation Failures:
	- Make sure the *Gateway VM has port 80 opened*
	- Check that the *Gateway VM* has the *correct time* and synchronising its time automatically to a *Network Time Protocol* (NTP) server

## Volume Gateway cache
- Cached mode: only the **most recent data** is stored
- Looking at *cache efficiency*
	- Look at the `CacheHitPercent` metric (you want it to be *high*)
	- Look at the `CachePercentUsed` (you *don't want it to be too high*)
- Create a larger cache disk
	- Use the cached volume to clone a new volume of a larger size
	- Select the new disk as the cached volume