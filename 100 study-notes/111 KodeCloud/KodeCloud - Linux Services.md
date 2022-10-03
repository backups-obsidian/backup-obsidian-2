---
created: 2022-09-14 00:22
updated: 2022-10-02 12:25
---
---
**Links**: 
- [[../104 Linux/104 Linux Index | 104 Linux Index]]
- [[111 KodeCloud Index]]

---
## Linux Services
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
> Now we have to `enable` the service to start at boot up.

- We can provide additional meta data information about the service using `Description` in `[Unit]`
- If there are commands or scripts that need to be run before or after starting the service then we can define `ExecStartPre` & `ExecStartPost`
- If we want the application to restart incase it crashes we can specify `Restart=always`
	- ![[attachments/Pasted image 20220914121227.png]]

- Above was an example for a simple unit service file. It can be much more complex. Docker service example
	- ![[attachments/Pasted image 20220914121333.png]]

### Diving deeper into services
- When a `systemd` system boots up, `systemd` is trying to make the system state match the state specified by `default.target` - which is usually an alias for either `graphical.target` or `multi-user.target`.
- `multi-user.target` *normally defines a system state where all network services are started up and the system will accept logins, but a local GUI is not started*. 
	- This is the typical *default system state for server systems*, which might be rack-mounted headless systems in a remote server room.
- `graphical.target` is another possible alias for `default.target`. Normally it's defined as a superset of the `multi-user.target`: it includes everything the `multi-user.target` does, plus the activation of a local GUI login.
- The line `WantedBy=multi-user.target` in a service tells `systemd` that this service should be started as part of normal system start-up. 
- However, `WantedBy` is separate from the enabled/disabled state: so in another sense, it's sort of a "preset": it determines under what conditions the automatic start may happen, but only when the service is enabled in the first place.
- *If you omit the `WantedBy=multi-user.target` line and no other enabled service includes a `Requires=your.service` or `Wants=your.service` in its service definition, your service will not be started automatically*.
- **`systemd` works on dependencies, and at boot time, if nothing `Requires` or `Wants` your service, it won't be started even if the service is enabled**.
- Sure, you could edit your `default.target` to add or delete `Requires` or `Wants` lines for any services you want started at boot time - but so that you can just drop a new service file into the system and have it work by default (which makes things very easy for software package managers), **`systemd` has the `WantedBy` and `RequiredBy` keywords which can be used to insert `Wants` and `Requires`-type dependencies (respectively) from "the other end"**.
- You should omit the `WantedBy` line if you _don't_ want the service to be ever started automatically at boot time, _or_ this service is a part of a chain of dependencies you've defined explicitly.
- For example, you might be refactoring server application A and for some reason or another decide to split some optional functionality off it into a separate service B, to allow the user the choice of not installing it if it isn't needed. 
- You could then make service B a separate `service-B.rpm`, and define `B.service` with `WantedBy=A.service` to make `systemd` start up service B automatically whenever service A is started - but only when `service-B.rpm` is actually installed.

> [!Note]- A `Wants` or `WantedBy` only says that the system should startup one service whenever another service or target is also started, but it *specifies nothing at all about the startup/shutdown order*. 
> If you need service B to be already running when service A starts up, you'd need to add `Before=A.service` in the `B.service` file to explicitly specify the start-up order dependency.

- [Why do most systemd examples contain WantedBy=multi-user.target? - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/506347/why-do-most-systemd-examples-contain-wantedby-multi-user-target) 

