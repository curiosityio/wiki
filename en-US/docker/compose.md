---
name: Compose
---

# Install

Don't install via apt-get. The version is too old.

Instructions [found in the docs](https://docs.docker.com/compose/install/)

```
curl -L "https://github.com/docker/compose/releases/download/1.11.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose; chmod +x /usr/local/bin/docker-compose
```

# File reference

```
version: '3' <--- docker compose version
services:
  web: <--- arbitrary name for service. Name whatever you want. Usually, 'web' is used for your code.
    build: . <--- tells compose to build the Dockerfile in this directory to create image.
    user: username <---- run 'command' as the user. Make sure in your Dockerfile you are creating the user!
    command: npm run start <---- command to run on this image once built. This works best for me instead of CMD in Dockerfile.
    ports: <--- Allows the host machine running this docker image to access port 5000 on this image via the host machine 80.
     - "80:5000"
    expose: <--- like 'ports' option, but will not expose to port on host machine. Just exposes port for other images to use. 
     - "5000"
    volumes: <--- connects the directory, /code, in the docker container to . in the host machine. Allows persistent storage.
     - .:/code
    restart: always <--- restart when it crashes
    environment:<---- sets environment variables in container.
      - FOO=bar
    links: <--- connects db and redis images below to this container. It starts these containers before starting this one. Note: It does not wait for these containers to finish setting up, just until they start. Like depends_on but also adds entries in the /etc/hosts file in this container to connect via network to those containers.
      - db
      - redis
  db:
    image: "postgres:9.6.2-alpine" <--- create container starting from this image.
    restart: always
    expose: <--- exposes this port to the internal network of the containers but does not expose to outside host network like 'ports' does.
        - "5432"
  redis:
      image: "redis:3.2.8-alpine"
      restart: always
      expose:
          - "6379"
      volumes:
          - /usr/local/var/db/redis/:/usr/local/var/db/redis/
```
