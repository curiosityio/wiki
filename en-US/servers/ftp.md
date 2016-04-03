---
name: FTP to server
---

By default FTP works on the server which is cool if you login via SSH.

Open filezilla, for host enter your ip address to server, username enter root, password enter server root password, port enter 22.

Then when you connect you will be in /root directory.

Done.

I like to create a system link for my clients who want to access their website files to be able to access thier website easier. `ln -s /var/www/yourdomainname.com /root/website` so now in filezilla, they will see a file named "website" they can click and see their website html files.
