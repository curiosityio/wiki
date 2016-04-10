---
name: Dockerize a NodeJS app
---

Resources:

* [Summary how to Dockerize nodejs app from Node's website (not production ready. Follow best practices to get closer.)](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)
* [Best practices Dockerize Nodejs app](https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md)
* [Node official Docker image](https://hub.docker.com/_/node/)

# Dockerize existing NodeJS Express API

Lets assume you already have a working NodeJS Express app. Lets get it to run in a Docker container.

* `cd /path/to/express/node/app`
* `touch Dockerfile`
* Open the `Dockerfile` and lets write some Docker commands:
```
# DockerHub image, node, to create our image from. Argon is version of node image. Argon is nodejs's v4.2.0 LTS.
FROM node:argon

# Create app directory. In our Docker container, where our express app will exist.
RUN mkdir -p /var/www/app
WORKDIR /var/www/app

# Install app dependencies. Copy package.json from our current directory into the Docker container.
COPY package.json /var/www/app/
RUN npm install # run this command in the container.

# Bundle app source. Copy all the rest of our express app into the docker container.
COPY . /var/www/app

EXPOSE 5000 # expose to the host machine what port our Express application is running on.
CMD [ "npm", "start" ] # command to run to start our express app.
```
* Build our Docker image:
```
docker build -t <your dockerhub username>/name-your-webapp .
```
* Done. Now we can run the image:
```
docker run -p 49160:5000 -d <your dockerhub username>/name-your-webapp
# -d runs the image as a background process to keep it going. -p binds our host machine's port, 49160, to the Docker container's port 5000.
```

* Now that it is running, you can check out the STDOUT logs of the container:
```
docker ps # to find the ID of the image we are running.
docker logs <container id of image we just started running>
# example output may be something like: Running on http://localhost:5000

# test container is working:
curl -i localhost:49160
# should get 200 success (unless your express app doesn't get a GET / call in it)

# if you need to get into the container and run some bash on it:
docker exec -it <container id of image you just started running> /bin/bash
```
