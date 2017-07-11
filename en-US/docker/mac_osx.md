---
name: Docker on OSX
---

Docker works on Linux. You can run Docker on Mac OSX by way of the [toolkit](https://docs.docker.com/engine/installation/mac/). This installs VirtualBox on your machine to create a Ubuntu VM to run docker through.

# Install

Download and install it: [Docker for Mac](https://www.docker.com/docker-mac)

Docker for Mac uses Hyperkit instead of VirtualBox to run. You should not need VirtualBox to get running. Docker toolkit does, so I do not recommend that method. 

# Docker compose on Mac

After installing [Docker for Mac](https://www.docker.com/docker-mac), Docker compose may not work completely yet. I was able to get some docker-compose containers going but not all of them.

I regularly was receiving the error: `ERROR: Couldn't connect to Docker daemon. You might need to start Docker for Mac.` while trying to run Docker Compose on my machine. As I said, I can get some docker-compose images running but not all. From [this GitHub issue](https://github.com/docker/compose/issues/4462) it turns out that there is a non-readable file in my source code directory that is causing compose to fail. The error messaging I was receiving was generic.

After setting permissions `chmod -R a+rX *` in my current directory, Docker compose successfully was able to run on my machine.

Some GitHub issues for this error say that you need to get `docker-machine` running (which requires VirtualBox and the whole bit) but that is false.
