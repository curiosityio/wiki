---
name: Hosting
---

# Firebase

### Custom domain Firebase Cloudflare

* When you create a custom domain in the Firebase dashboard, it will have you create a couple TXT records. Add those to your DNS.

* All DNS records that you use to host your site on Firebase (CNAME records, TXT records, A records), do *not* use DNS and HTTPS proxy through Cloudflare. Yes, even `www` CNAME.

* After Firebase installs SSL cert, it will ask you to install an A record or CNAME. I go with A record. And again, DNS only for Cloudflare. *not* HTTPS proxy.
