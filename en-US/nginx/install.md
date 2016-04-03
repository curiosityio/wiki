---
name: Install NGINX
---

# Install
`sudo apt-get update; sudo apt-get install -y nginx;`

Confirm that nginx will restart after server restart `sudo update-rc.d nginx defaults`

Done. Test it by going to your web browser and entering `http://serverIpAddressHere` (`ifconfig` command gets IP address if needed under `eth0`) and you should see a message "Welcome to NGINX".

Good docs to check out after this:

* [FTP into server]('../servers/ftp')
* [custom domain/subdomain setup]('proxy_site')
* [Host static website]('host_static')
