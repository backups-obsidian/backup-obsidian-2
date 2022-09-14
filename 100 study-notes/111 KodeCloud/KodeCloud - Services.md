---
created: 2022-09-14 00:22
updated: 2022-09-14 11:48
---
---
**Links**: [[111 KodeCloud Index]]

---
## Services
- **Services in linux help us configure software that run in the background**.
	- Services make sure that the software runs all the time automatically when the servers are rebooted and follow the right order of startup.
	- *Web server or database server are example of services*.

- When we install a web server or docker they are automatically configured as services.
- *Starting the services*
	- `sudo systemctl start <service-name>`
	- `sudo service <service-name> start`
	- Service command uses `systemctl` underneath.
- *Stopping a service*
	- `sudo systemctl stop <service-name>`
- *Check the status of the service*
	- `sudo systemctl status <service-name>`
- Configure a service to start automatically when the system boots up
	- `sudo systemctl enable <service-name>`
	- We use `disable` if we don't want the service to start at startup

### How to configure programs as a service
- **`systemctl` program is used to manage systemd services**.
	- So to use our program as a service it has to be configured as a systemd service.

> [!important]- A systemd service is configured using a systemd *unit file* which may be located at `/etc/systemd/system`

- The unit file must be named after the name of the service and must have `.service` extension
	- ![[attachments/Pasted image 20220914114003.png]]
- `ExecStart` directive is where we list the command that we want to run as a service.
- We need to *reload the systemd daemon to let systemd know that we have configured a new service*.
	- `sudo systemctl daemon-reload`
- The final step is to start the service

> [!question]+ How do we configure the service to run automatically when the system boots up?
> We need to make the following changes to the service file
> ![[attachments/Pasted image 20220914114616.png]]
> This means that the service will run after the `multi-user` service.
> Now we have to `enable` the service to start at boot up.

