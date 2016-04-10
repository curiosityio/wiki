---
name: Docker
---

# Lingo

`image`: A docker image is a file that you can create containers from. An example of an image is the ubuntu image. Images are great because they are immutable. I can create 100 docker containers from the ubuntu image for example and all 100 have thier own file system, processes, etc like a sandbox environment.

Docker images are created from a Dockerfile telling the image what to do. You can create your own docker images by creating a Dockerfile.

There are official docker images for Ubuntu, node, postgres, and hundreds of others. This is great because if you want to use a tool like PHPMyAdmin you do not need to download and install the software on your machine. Just pull the image, create a container from that image, then use it on your machine. When you're done, delete the container and it was like nothing happened on your host machine.

`Dockerfile`: a 'makefile' for creating Docker images. You tell it what commands to run, what environment variables to create, what files to copy from your host OS to the container, etc.

`container`: A docker container is created from an image. A container is a 'sandbox' if you want to call it where it has it's own filesystem, processes, users, etc. You can create 100 separate containers from the 'ubuntu' image and you now have 100 ubuntu instances running all separated from each other.

You can connect host ports to Docker container ports. You can mount host directories to Docker container directories. This is the way you make containers do real changes to your OS. If you do not connect ports or directories, you can go into a docker container, create tons of files, delete whatever you want then when you exit, it did nothing to your host OS.

# Common commands

* Run some commands inside of an already running Docker container:
```
docker exec -it <container id> /bin/bash
```

* View all running Docker containers:
```
docker ps
```

* View all Docker containers no matter if running or not:
```
docker ps -a
```

* Create *and run* a docker container from an image (must include an image to create a container):
```
docker run -it --name name-here ubuntu /bin/bash
# --name name-here is to allow you to name container for use later when you use `docker ps -a`. If you don't provide a name, Docker will create a random one for you.
```

* Linking a docker container to another docker container:
```
docker run --name name-here --link other-container-name:alias-name -p 80:80 -p 443:443 image-name-such-as-ubuntu
# --link links an already running docker container to this container with the alias hostname 'alias-name'. Whatever you put for 'alias-name' your docker container can use that hostname to communicate with it.
# If you want to want to run multiple containers together and get them all running together use Docker Compose. 
```
