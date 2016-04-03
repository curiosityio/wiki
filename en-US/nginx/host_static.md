---
name: Host static websites
---

If you just want to throw up a static HTML/CSS/JavaScript website, NGINX is super easy for that.

```
cd /var
mkdir www
cd www
mkdir nameOfWebsite.com
cd nameOfWebsite.com
```
The directory: `/var/www/nameOfWebsite.com` will be where you put all of your website code into. (if you do not have website code yet: `echo "hello" > index.html` is good enough to test with). Check out docs on [FTP]('../server/ftp') to FTP into server and drop website files.

Now `nano /etc/nginx/sites-available/default` and edit the line: `root /usr/share/nginx/html;` to instead be: `root /var/www/nameOfWebsite.com`. (in this situation to just get a site up and running fast, editing `/etc/nginx/sites-available/default` is fine. Check the docs on [NGINX site config file]('proxy_site') to create multiple websites)

Restart nginx: `service nginx restart` and then if you go back to http://serverIpAddressHere you should see "hello" on your screen.

Done. For a custom URL instead of http://serverIpAddressHere, check out docs on [NGINX proxying websites]('proxy_site').

By default FTP works on the server which is cool. open filezilla, for host enter your ip address, username enter root, password enter droplet password, port enter 22. Then when you connect you will be in /root directory. I like to create a system link for the client to be able to access thier website easier. `ln -s /var/www/yourdomainname.com_website /root/website` so now in filezilla, they will see a file named "website" they can click and see their website html files.
