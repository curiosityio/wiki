---
name: Do right away after creating a new server
---

* Install git `sudo apt-get update; sudo apt-get install -y git;`
* Assuming you have the root username/password to get into the server, run this command to add SSH login support `cat ~/.ssh/id_rsa.pub | ssh root@your.ip.address "cat >> ~/.ssh/authorized_keys"` to add my ssh key to server.

# Create a new user with root privileges

* Create Linux user and add to sudo group

```
adduser username
```

You will be asked a few questions, starting with the account password.

Enter a strong password and, optionally, fill in any of the additional information if you would like. This is not required and you can just hit "ENTER" in any field you wish to skip.

```
gpasswd -a username sudo
```

Your new user now has sudo privileges.

* Add SSH login to this new user.


```
su - username
mkdir .ssh
chmod 700 .ssh

nano .ssh/authorized_keys
```

Paste in your public key located on your machine. Save and exit nano.

```
chmod 600 .ssh/authorized_keys
exit
```

Now you may `ssh username@ip.address.here` to login without password.

# Create a basic firewall

```
sudo apt-get install -y ufw

sudo ufw status # check status of ufw. should show inactive when first install.
```

If you have IPv6 installed on your server, then make sure "IPV6" is set to "yes", like so:

```
sudo nano /etc/default/ufw
```

make sure `IPV6=yes`.

Save and quit. Then restart your firewall with the following commands:

```
sudo ufw disable
sudo ufw enable
```

Now it's time to handpick what is allowed in and out of server.

```
sudo ufw default deny incoming
sudo ufw default allow outgoing

sudo ufw allow ssh
sudo ufw allow ftp
sudo ufw allow http # or sudo ufw allow 80
sudo ufw allow https # or sudo ufw allow 443
sudo utf allow 5432 # postgres
```

These commands add access from ALL IP incoming IP addresses. You may want to only allow postgres connection from your IP but this is simple to do.

Here how to allow from only IP address:

```
sudo ufw allow from 192.168.255.255 ports 5432
```

Lets enable it now.

```
sudo ufw show added # show all added
sudo ufw enable # make sure you've added all you need before enabling!
```

*Tips:*

* Delete rule:
If you need to delete a rule from messing up something: `sudo ufw delete allow ssh`

or do alternative: `sudo ufw status numbered` which will show list of current rules. Then issue `sudo ufw delete numberHere` to delete a rule from list.

* Reset everything: `sudo ufw reset`

* Disable firewall: `sudo ufw disable`

# fail2ban

You may want to enable fail2ban on your system which will deny bots from trying to brute force into your server. [Here](https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-ubuntu-14-04) is a DigitalOcean doc on how to set it up. 
