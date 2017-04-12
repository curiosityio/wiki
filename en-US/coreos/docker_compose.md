---
name: Docker Compose
---

# Install Docker Compose on CoreOS

CoreOS is a read-only file system (for the most part) so it is a little different installing Docker Compose. Traditionally, you install Docker Compose in the /usr/local/bin directory but that is read-only on CoreOS. So, we will install to /opt which is writable.

```
$> sudo su
#> mkdir -p /opt/bin; curl -L "https://github.com/docker/compose/releases/download/1.11.1/docker-compose-$(uname -s)-$(uname -m)" -o /opt/bin/docker-compose; chmod +x /opt/bin/docker-compose;
#> exit
```
