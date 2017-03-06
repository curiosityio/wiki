---
name: SSL
---

# Get free SSL cert via Let's Encrypt

LetsEncrypt.org allows you to get a free SSL cert. It's free, automated, and auto renews if you set it up that way.

I am going to use [Certbot](https://certbot.eff.org/), also made by the EFF, to setup my Let's Encrypt SSL as it's super easy.

* Go to [Certbot site](https://certbot.eff.org/) and tell it what web server you run and OS. I chose Nginx and Ubuntu 14.04 for this example. Other versions of Ubuntu might even be easier so use the site to check.

* Download the script to a directory on your server.

```
cd
mkdir -p ssl/certbot/
cd ssl/certbot/
wget https://dl.eff.org/certbot-auto
chmod a+x certbot-auto
```

* Configure your APIs and web servers for verification.

Certbot does HTTP verification. It will generate a random file, put it in your API or website files so it is publicly visible, then if it can read that file, you have verified your domain.

If you have a static file website, you don't have to do anything. As long as the web server is running, verification will be easy for you.

If you have built a Node app with Expressjs for example, you will need to configure Express to serve static files so when you go to `http://api.whatever.com/.well-known/foo.txt` it will not 404 on you.

Add this line to your server js file to tell Express to serve static files:

```
app.use('/.well-known', express.static('.well-known')); // static fileserver for let's encrypt.
```

This tells Express to serve the files in `./.well-known` (it's a relative path) to the directory `http://api.whatever.com/.well-known`.

* Now, generate the SSL certificates:

```
./certbot-auto certonly --webroot -w /var/www/example -d example.com -d www.example.com -w /var/www/thing -d thing.is -d m.thing.is
```

As you can see, you can add as many domains and websites as you wish.

This command will generate the verification files, put them inside of `/var/www/example/.well-known/` and do the verification for you. If it fails, it usually has some good error messages saying what HTTP response code it got back.

* Point Nginx to your newly created certificates:

```
cd /etc/nginx/sites-available/
nano api.whatever.com.conf
```

And add the following to your config file:

```
####################################
# The below should already be there.
####################################
server {
    listen         80;
    server_name    api.whatever.com;

    location / {
        proxy_pass http://localhost:5050;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

####################################
# This is new unless you had SSL installed previously.
####################################
server {
    listen 443 ssl;

    server_name api.whatever.com;
    ssl on;
    ssl_certificate /etc/letsencrypt/live/api.whatever.com/cert.pem;
    ssl_certificate_key /etc/letsencrypt/live/api.whatever.com/privkey.pem;

    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
    ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';

    location / {
        proxy_pass http://localhost:5050;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Restart Nginx after done:

```
service nginx restart
```

You should see it restart with status `[ OK ]` but if it fails, check the logs:

```
cat /var/log/nginx/error.log
```

Go to your wesite: `https://api.whatever.com` and it should have SSL installed successfully now!!

* Now for auto renew.

Do a dry run to see if autorenew works successfully for certbot:

```
./path/to/certbot-auto renew --dry-run
```

If it is successful, you can create a crontab to run it each day:

```
crontab -e
```

...and when the text editor shows up, add a line to the end of your file:

```
0 5 * * * /root/ssl/certbot/certbot-auto renew --quiet --no-self-upgrade
```

This will run at 5am each day 1 time. 
