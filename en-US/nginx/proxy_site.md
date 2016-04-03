---
name: Custom URL, subdomains NGINX by setting up a reverse proxy with NGINX
---

When you want to use a custom URL or a subdomain for your website, you use a "reverse proxy" through NGINX which technically redirects the client's request to another running server on your machine.

For example, you are using nodejs. Nodejs is a web server on it's own. It responds to HTTP requests like any other web server. However, nodejs runs on a random port such as `5000` and does not have the capabilities to setup custom domains/subdomains. That is why on your server you will have your node server running on port 5000 and then when a user goes to http://yourwebsite.com NGINX will get that request (as NGINX is listening on port 80) and will proxy that request to the node server running on port 5000 for it to respond to the client.
...actually, nodejs can respond to custom domain requests (see ghost blog setup) but NGINX is simple to use and works well. I also like using it as you can eventually use it for load balancing, gzip, etc. Reverse proxy servers to your main application are good.

Steps to setting up a custom domain:

* [setup A record in DNS records](#setup-dns)
* [NGINX site config file](#nginx-site-config-file)


# Setup DNS

Go to your domain registrar's DNS settings and edit the DNS file to look something like this:

![](/docs/images/dns_custom_domain.png)
> Change the values of the IP address to your own server. `ifconfig` > `eth0` to find it.

blog, help, wiki <-- those are all subdomains.
@, www <--- those are for the main website. www points to @ as you can see.

# NGINX site config file

`/etc/nginx/sites-available/` are where all nginx site config files live. Go to this directory: `cd /etc/nginx/sites-available`.

Create a file for your website:
```
touch yourwebsite.com.conf # or subdomains: wiki.yourwebsite.com.conf
```

Inside of that file, here is the basic structure for a custom domain website that will reverse proxy to your website:
```
server {
    listen         80;
    server_name    yourwebsite.com; # you can also use subdomains here.

    location / {
        proxy_pass http://localhost:5556; #change port number to your applications port.
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```
This file is telling NGINX to listen on port 80 to yourwebsite.com requests and then reverse proxy those requests to localhost:5556 which is where a nodejs server, peachdocs server, etc will be listening and respond to the connection.

If you need SSL, check out docs on [SSL site config]('ssl').

To enable this website, link it to sites-enabled directory and then restart nginx:
```
sudo ln -s /etc/nginx/sites-available/yourwebsite.com.conf /etc/nginx/sites-enabled/yourwebsite.com.conf

service nginx restart
```
Wait a few hours for DNS records to propigate and then you should be able to go to http://yourwebsite.com and see your website.
