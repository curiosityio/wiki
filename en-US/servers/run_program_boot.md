---
name: Run a program on boot.
---

When your server restarts, hopefully all of your webserver, scripts, etc. are set to startup automatically. If not, lets set it up.

# Create startup script.

Bash scripts are where we write the code to start our programs.
```
cd
touch node_sites_startup.sh
chmod +x node_sites_startup.sh
```
You can name your startup script whatever you want. This one should start node sites. You can create one for peachdocs if you like too.

Install npm forever module

```
sudo npm install -g forever
```

Contents of node_sites_startup:
```
#!/bin/sh

if [ $(ps -e -o uid,cmd | grep $UID | grep node | grep -v grep | wc -l | tr -s "\n") -eq 0 ]
then
    export PATH=/usr/local/bin:$PATH
    export NODE_ENV=production
	forever start --sourceDir /var/www/ghostblog index.js >> /root/ghost.log 2>&1
    forever start --sourceDir /var/www/api app/server.js >> /root/api.log 2>&1
fi
```
This starts 2 forever scripts. The syntax of `forever start --sourcedir ...` is [forever](https://github.com/foreverjs/forever) syntax.

You can run this file directly to test if it works: `sh ~/node_sites_startup.sh`

*Important: Keep these tips in mind*

* If any of your applications require environment variables, you must set them in the startup script. `cron`, which we will use to run our bash scripts, does not have any environment variables involved. For example, an API uses variable `POSTGRES_DATABASE_URL` variable inside of the API code to connect to the database, you have to set the `export POSTGRES_DATABASE_URL=aldsfasdof` inside of this file (above you see one export variable. Put it there above your forever scripts). I also set them in the `~/.bashrc` file as well.
* `export PATH=/usr/local/bin:$PATH` is there for the same reason as the point above. cron has no environment variables. This allows us to say `forever` without giving the full path: `/usr/local/bin/forever`.


To give an example of something other then forever, here is another for peachdocs:
```
#!/bin/sh

if [ $(ps -e -o uid,cmd | grep $UID | grep node | grep -v grep | wc -l | tr -s "\n") -eq 0 ]
then
	cd /path/to/peach/site.peach
	/usr/bin/peach web &
fi
```

# Run startup script on boot.

Use cron to run them on boot.
```
crontab -e

# then add this line at the end of the file:
@reboot /root/node_sites_startup.sh #or whatever your file name is.
# save and exit.
```

Reboot your machine to test it works:
```
reboot
```
