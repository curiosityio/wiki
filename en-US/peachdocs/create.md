---
name: Create new peachdocs site
---

Follow this whole file to create a new site.

# Create site

Peachdocs is installed, time to create a new site.

Create a directory where the sites are going to exist. It does not matter where.
```
cd
mkdir peachdocs-sites
cd peachdocs-sites
```
The above commands are optional. I like to have all of my peachdocs websites (if you have multiple) all in one directory which is what I am doing here.

Time to actually create the peachdocs site:
```
peach new -target=peachsitenamehere.peach # name it whatever you want, make sure to have .peach at the end of name.
cd peachsitenamehere.peach
```
When asked if want to use custom templates, I say 'n' for no. You can use your own templates later on so you're not stuck with the choice. This at least creates a starting point for you so you have something to work with.

# Configure site.

Time to configure it to finish creating site.
```
git clone https://github.com/peachdocs/peach.peach.git custom
nano custom/app.ini
```
In this file, put in the contents below and change the values around to work for your project.
```
RUN_MODE = prod
HTTP_PORT = 5556 # port to run peachdocs on.

[i18n]
LANGS = en-US
NAMES = English
# I only support English so this is good.

[site]
URL = http://yourwebsitehere.com

[page]
HAS_LANDING_PAGE = false
# If you want to have a home page, change above to true. This is to jump directly to documentation page of website and skip home page.

[docs]
TYPE = remote
TARGET = https://github.com/yournamehere/yourprojecthere.git
SECRET = createRandomlyGeneratedStringHereForSecret
```
This file sets up the peachdocs website. We say what port to run on, it is has a home page or just jumps to the documentation page, and sets up the git repo.

Here we are accessing a remote git repo. You can replace this part to access a local git repo if it's local.
```
RUN_MODE = dev
# notice how I am setting RUN_MODE here as well. dev will refresh grabbing the docs every time you refresh the website for local repos this is ok.

[docs]
TYPE = local
TARGET = /path/to/git/repo
```

We are not done yet. We also have to setup webhooks for git to tell us when to refresh the site as our git remote server has new code.

If you used a local git repo instead of remote, you can skip the webhooks part and delete the SECRET line from the `custom/app.ini` file.

# Webhooks

Webhooks are awesome because when we push new markdown files to our remote git server, peachdocs, if running, will receive a notification via webhook that the documentation updated and it will update the website docs for us!

In our `custom/app.ini` file where we talk about a SECRET. here is where the comes in handy.

In GitHub (As of right now, GitHub is the only git hosting provider that works. I have tried others but no luck. May have to wait until gogs is more stable and just use that.) go to your wiki git repo > settings > Webhooks & services. Create a webhook:

![](/docs/images/github_webhook.png)

Of course replace the `http://peachdocs.org` part with your own website, but you get the idea. With the URL: http://peachdocs.org/hook?secret=mypeach you see the `?secret=` at the end. This is where you put SECRET from the `custom/app.ini` file. I created a long randomly generated one (lowercase/uppercase alphabet with numbers) and stuck that in the `custom/app.ini` file as well as in the webhooks url appended to `?secret=`.

One last thing:
```
rm -rf data/docs
```
I have found that if you leave the default /data/docs directory in place and then update your git repo, nothing happens. You have to start clean and fresh and then everything works.
