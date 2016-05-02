---
name: SSH info
---

# Create SSH key

`ssh-keygen -t rsa -C "youremail@you.com"` then enter a passphrase (I generate a long one using lastpass and save it there. Don't lose the password!!!) and then it will create a ~/.ssh/id_rsa and ~/.ssh/id_rsa.pub ssh key.

# Send SSH key to remote server

* Assuming you have the root username/password to get into the server, run this command to add SSH login support `cat ~/.ssh/id_rsa.pub | ssh root@your.ip.address "cat >> ~/.ssh/authorized_keys"` to add my ssh key to server.
