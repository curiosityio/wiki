---
name: Do right away after creating a new server
---

* Install git `sudo apt-get update; sudo apt-get install -y git;`
* Assuming you have the root username/password to get into the server, run this command to add SSH login support `cat ~/.ssh/id_rsa.pub | ssh root@your.ip.address "cat >> ~/.ssh/authorized_keys"` to add my ssh key to server. 
