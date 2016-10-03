---
name: Cabot
---

Cabot is an open source Self-hosted, easily-deployable monitoring and alerts service - like a lightweight PagerDuty. It's alternatives are very complex to setup and get going. Cabot is quick, simple, easy to deploy and use.

Set it up on 1 machine and be able to monitor your servers on all of your other VPSs.

# Install and get up and running.

For now, Cabot uses the [Fabric](http://www.fabfile.org/installing.html) tool for deploying Cabot to your server environment. The team is working on a Docker use and may remove support for Fabric.

If you have done so, install Fabric:

```
sudo pip install fabric
```

Then follow this quickstart guide on your dev machine *not your server*. We will deploy to your server from your dev machine using Fabric.

```
git clone git@github.com:arachnys/cabot.git
cd cabot

cp conf/production.env.example conf/production.env
```

Open up `conf/production.env` and edit the environment variables to your needs. The environment variables are what sets up Cabot.

Walk through this file:

```
# Plugins to be loaded at launch
CABOT_PLUGINS_ENABLED=cabot_alert_hipchat==1.7.0,cabot_alert_twilio==1.1.4,cabot_alert_email==1.3.1

DATABASE_URL=postgres://cabot:cabot@localhost:5432/index
DJANGO_SETTINGS_MODULE=cabot.settings
HIPCHAT_URL=https://api.hipchat.com/v1/rooms/message
LOG_FILE=/dev/null
PORT=5000
VENV=/home/ubuntu/venv

# You shouldn't need to change anything above this line

# Local time zone for this installation. Choices can be found here:
# http://en.wikipedia.org/wiki/List_of_tz_zones_by_name
TIME_ZONE=America/Chicago

# Django admin email
ADMIN_EMAIL=you@change.this.email.com
CABOT_FROM_EMAIL=you@change.this.email.com

# URL of calendar to synchronise rota with
CALENDAR_ICAL_URL=http://www.google.com/calendar/ical/example.ics

# Django settings
CELERY_BROKER_URL=redis://:changemetorandomlygeneratedpassword_nospecialchars@localhost:6379/1
DJANGO_SECRET_KEY=changemetorandomlygeneratedpassword_nospecialchars

# Hostname of your Graphite server instance (including trailing slash)
GRAPHITE_API=http://graphite.yourdomain.com/
GRAPHITE_USER=levi
GRAPHITE_PASS=changemetorandomlygeneratedpassword

# From parameter for the graphite request. If not defined, by default take -10 minutes
# GRAPHITE_FROM=-10minute

# User-Agent string used for HTTP checks
HTTP_USER_AGENT=Cabot

# Hipchat integration
HIPCHAT_ALERT_ROOM=48052
HIPCHAT_API_KEY=your_hipchat_api_key

# Jenkins integration
JENKINS_API=https://jenkins.example.com/
JENKINS_USER=username
JENKINS_PASS=password

# SMTP settings
SES_HOST=email-smtp.us-east-1.amazonaws.com
SES_USER=awsSES_smtp_username_you_create
SES_PASS=awsSES_smtp_password_for_username_you_create
SES_PORT=465

# Twilio integration for SMS and telephone alerts
TWILIO_ACCOUNT_SID=your_account_sid
TWILIO_AUTH_TOKEN=your_auth_token
TWILIO_OUTGOING_NUMBER=+14155551234

# Used for pointing links back in alerts etc.
WWW_HTTP_HOST=cabot.yourdomain.com
WWW_SCHEME=http

# Use for LDAP authentication
AUTH_LDAP=true
AUTH_LDAP_SERVER_URI=ldap://ldap.example.com
AUTH_LDAP_BIND_DN="cn=Manager,dc=example,dc=com"
AUTH_LDAP_BIND_PASSWORD=""
AUTH_LDAP_USER_SEARCH="ou=People,dc=example,dc=com"
```

Now time to setup your system by installing all of the programs on the machine:

```
fab provision -H root@your.server.hostname
```

*Note:* I had an issue with this working. I had this error thrown `nodejs : Conflicts: npm` But when I went to the server and ran `nodejs -v` and `npm -v` they were both the latest versions LTS from node's website. Therefore, they were both installed correctly and fully up to date. To remove this error, I went into `bin/setup_dependencies.sh` and towards the top there is a list of `packages` and inside you see node and npm. Remove the line `npm` from this list and try this `fab` command again.

After provision is successful, you are ready to deploy to the server.

```
fab deploy -H ubuntu@your.server.hostname
```

Yes, you are supposed to use the username `ubuntu` here.

After this is complete, time to create your admin account for cabot.

```
fab -H root@your.server.hostname create_user:"username,password,email"
```

Of course, edit username, password, and email with your values you want.

By default, Cabot creates a config file for nginx on your server to host cabot. It's `/etc/nginx/sites-available/cabot`. I went inside of there and added a servername line for a subdomain.

```
server {
  listen 80;
  server_name cabot.curiosityio.com;
  ...
}
```

Done! 
