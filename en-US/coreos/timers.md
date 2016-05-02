---
name: CoreOS timers
---

CoreOS does not use cronjobs. They use system timers.

# Create a new system timer.

[official docs](https://coreos.com/os/docs/latest/scheduling-tasks-with-systemd-timers.html)

Here is an example. We want to run a docker image every day at 2am that performs a docker volume backup to S3.

* Go into your home directory or wherever you want and create a bash script that does all the heavy lifting of backing up to S3.

I am using a docker image [dockup](https://github.com/tutumcloud/dockup) (docs on how to use [here](../docker/backup)) to do my backups. I created this file in: `/home/core/dockup/backupall.sh` that I will add more lines too for every docker container I want to backup to. Here I am backing up the volumes for `taiga_taigaback_1`

```
#!/bin/bash

/usr/bin/docker run --rm --env-file /home/core/dockup/taiga-back-dockup.txt --volumes-from taiga_taigaback_1 --name dockup tutum/dockup:latest
```

* Create a system service. Timers run services.

```
sudo su - # must be root to create a service.

cd /etc/systemd/system
vi dockup-backupall.service
```

The contents of `dockup-backupall.service`:

```
[Unit]
Description=Backup docker volumes to S3.
Requires=docker.service # require docker in order to run.

[Service]
Type=oneshot # only run this once.
ExecStart=/usr/bin/sh /home/core/dockup/backupall.sh # run bash script.
```

* Create a timer to run the service every day at 2am:

```
vi dockup-backupall.timer # make sure same name as the service we created.
```

*Note: if you decide to create a timer of a different name then the service file name, add this to your .timer file:*

```
Unit=date.service # name of service file.
```

Contents of timer file:

```
[Unit]
Description=backup oneshot
Requires=docker.service # requires docker to run

[Timer]
OnCalendar=*-*-* 02:00:00 # Run every day at 2am
```

* Install our timer to the system:

```
systemctl start dockup-backupall.timer
```

* Done. Here are a couple more commands that come in handy:

##### Check logs to see if timer ran:

```
journalctl -f -u name-of-your-service-file-you-created-here.service
```

##### After you edit a timer file, you need to reload it into the system for changes to take effect:

```
systemctl daemon-reload
```

##### List all system timers installed on system

```
systemctl list-timers --all
```
