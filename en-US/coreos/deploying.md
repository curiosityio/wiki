---
name: Deploying
---

# Create SSH keys for git deployment keys

Generating SSH keys on CoreOS is the same as you would on other Linux distros: `ssh-keygen -f $HOME/.ssh/id_rsa -t rsa -N ''`

You can then view the ssh key: `cat ~/.ssh/id_rsa.pub`
