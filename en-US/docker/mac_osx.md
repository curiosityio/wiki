---
name: Docker on OSX
---

Docker works on Linux. You can run Docker on Mac OSX by way of the [toolkit](https://docs.docker.com/engine/installation/mac/). This installs VirtualBox on your machine to create a Ubuntu VM to run docker through.

# Install

The [official docs](https://docs.docker.com/engine/installation/mac/) go over installation in detail. Here is the short version:

* Go to the [docker toolbox download page](https://www.docker.com/products/docker-toolbox) to download the toolbox for Mac. Then follow the installation wizard after download.
* Open launchpad > Docker Quickstart terminal. This will bring up terminal and start to create a 'default' VirtualBox VM for docker to run in.
*Note: This command may hang. Give it a while to run but it may hang as I have had issues in the past with it hanging while creating the VM. If it does hang, CTRL+c out of the quickstart terminal, run the following commands below, then open up the Docker Quickstart termianl again:*  
```
docker-machine rm -f default
rm -rm ~/.docker/machine
docker-machine -D create -d virtualbox default
```

* Docker should now be installed successfully. Test to verify it is up and running:
```
docker run hello-world
```
