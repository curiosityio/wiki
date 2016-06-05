---
name: Deploy code to server
---

There are many ways to deploy code to servers. You can [FTP]('ftp') code over to your server, use [Docker](https://www.docker.com/), or [git deploy](#git-deploy). Methods can be very simple to very complex. There is no right way to deploy, find what works best for the project.

# Git deploy

Your source code is already hosted in a remote server. You work on the code on your dev machine and push it up to a remote server where the 'master' branch is in tip top shape. Make deploys easy by doing a `git pull` to grab the latest version of your code and do a deploy that way.

First, we need to create an ssh key and add it to our GitHub, GitLab, BitBucket, Gogs, etc. account as a deploy key in order to pull code. [SSH docs]('ssh') covers that.

Cool, now lets add the ssh key to our ssh-agent on our server so that anytime you want to use the ssh key you dont need to type a password in:
```
eval `ssh-agent`
ssh-add /root/.ssh/id_rsa
```
Enter passphrase that you used to generate the ssh key, and you're done.
(I have found that after you exit the SSH session on your machine and then go back into it you have to do this all over again so still kind of annoying).

Copy the SSH key:
```
cat ~/.ssh/id_rsa.pub
```
Highlight the output and copy it. Go to your git repo on GitHub, GitLab, Gogs, BitBucket, etc. and add this copied SSH key as a deploy key. This gives your server access to the git repo and allows you to do a git pull.

Clone down the git repo (*Note: You must use HTTPS git link for clone. SSH denied for deploy keys*), and then anytime you want to do to a deploy you just do a `git pull`. 
