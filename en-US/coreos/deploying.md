---
name: Deploying
---

# Create SSH keys for git deployment keys

Generating SSH keys on CoreOS is the same as you would on other Linux distros: `ssh-keygen -f $HOME/.ssh/id_rsa -t rsa -N ''`

You can then view the ssh key: `cat ~/.ssh/id_rsa.pub`

# Setup CoreOS to start Docker automatically on reboot.

I do not know why my CoreOS stable instances I create via DigitalOcean do not start Docker automatically on reboot but they do not. Whenever the CoreOS machine will do a reboot (which automatically happens when CoreOS does a system update), my machine will go down and Docker will not restart when the machine comes back up resulting in my containers not starting automatically (assuming I set `restart=always` when I ran the `docker` command or in my docker compose file(s)).

```
sudo su; systemctl enable docker.service; systemctl start docker.service;
```

[Thank you to this GitHub issue](https://github.com/docker/compose/issues/3241#issuecomment-206925136) for helping me solve this issue. As described in the issue:

```
I am using Docker Compose 1.5.2 on CoreOS stable (835.13.0), and I notice that if I have restart: always in my docker-compose.yml file, the containers don't start automatically upon reboot.

However, as soon as I login and issue a docker command (such as docker ps or even docker version) the containers start up as expected. It's like I have to call docker once before compose kicks in.
```

That is what I was going through. This command seems to fix it for me. I would check the logs in my CoreOS machine to find that after a reboot, the Docker engine would not start. Then when it did (after I issued a `docker ps` command) it would show in the logs a reboot. 

# Setup CoreOS to run Docker system prune periodically

Docker can quickly generate a lot of disk space on your machine. To help limit this, let's run a periodic task (like cron) but deletes unused data on the system. 

*Note:* Yes, this script below could be bad. What if something is wrong with my Docker, containers do not run, and then they call get deleted when the script runs? Yes, that could happen. However, Docker containers should not be created in a way that they are critical to stay alive. They should be configured in a way that if you lost all of them, it would not be a big deal as you could just create new containers. 

* Create a service file: `sudo vi /etc/systemd/system/docker-prune.service` and paste in the following:

```
[Unit]
Description=Runs docker prune to clean up docker
After=docker.service
Requires=docker.service

[Service]
Type=oneshot
ExecStart=/usr/bin/docker system prune --all -f
```

* Create a timer file: `sudo vi /etc/systemd/system/docker-prune.timer` and paste in the following: 

```
[Unit]
Description=Run docker prune.service periodically

[Timer]
OnCalendar=*-*-* 02:00:00
```

* Start the timer: `sudo systemctl start docker-prune.timer`

# Enable Docker logs rotation

[Check out the docs](https://success.docker.com/article/how-to-setup-log-rotation-post-installation) to do this.