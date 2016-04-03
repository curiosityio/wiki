---
name: Go lang
---

[go lang](https://golang.org/)

# Install

*Note:* directions for go lang v1.6 directions. Check [go lang](https://golang.org/) website to see latest version. They have helpful install documentation as well. I am sure install directions will be same as below.

Lets install the go lang binary to our system.
```
cd

mkdir golang
cd golang

sudo curl -O https://storage.googleapis.com/golang/go1.6.linux-amd64.tar.gz

sudo tar -xvf go1.6.linux-amd64.tar.gz

sudo mv go /usr/local
```
Great. Now we need to add go to our PATH variable for system wide access.

```
nano ~/.bashrc

# add these lines below to this file:
export PATH=$PATH:/usr/local/go/bin
export GOPATH=$HOME/go

# save and exit.

source ~/.bashrc
```
As you can see from the lines that we added above, we have a GOPATH. This tells go where we want go to store files such as packages, installing programs, etc. We have it set to $HOME/go. We need to make this directory.
```
cd
mkdir go
```

Done. Test to make sure it works:
```
go version
```
Should see a version print out. 
