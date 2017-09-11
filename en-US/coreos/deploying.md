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
