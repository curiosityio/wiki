---
name: Docker hub
---

# Push new Docker image to Docker pub as public image.

* Create a new GitHub repo (yes, you can use an existing repo if you want. Perhaps a collection of Dockerfiles you like to use? Doesn't matter. This is just a place to store your Dockerfile.).

* [Create a new Docker Hub repo](https://hub.docker.com/add/repository/).

* Create a `Dockerfile` in your GitHub repo (yes, your GitHub repo is going to be very boring).

* Write all your code in your `Dockerfile` that you need.

* Build your `Dockerfile` into a Docker image: `docker build -t <docker-hub-username>/<name-of-docker-repo-on-hub>:latest .`

`:latest` is the tag. If you want to version control your Docker Hub images, I recommend making a release of `:latest` as well as `:0.6.1` or whatever your version you want is.

Make sure that your Docker image builds successfully.

* Login to your Docker Hub account on your machine: `docker login`

* Push up your newly built Docker image to Docker Hub: `docker push <docker-hub-username>/<name-of-docker-repo-on-hub>:latest`
