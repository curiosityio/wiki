---
name: Install
---

Peachdocs runs on [go](https://golang.org/). Make sure to [install go first]('../languages/go') and then install peachdocs.

Head to the [peachdocs github releases page](https://github.com/peachdocs/peach/releases), right click on the latest release's 'linux_amd64.zip' file, "Copy link address".

Now on our server, lets download the file.
```
cd
mkdir peachdocs
wget <paste here the link address you copied from GitHub above>
unzip linux_amd64.zip
```
To install it, just make the `peach` executable system wide:
```
sudo ln -s $HOME/peachdocs/peach /usr/bin/peach
```
Test to make sure it works:
```
peach -v
```
Should print out version code. At this point in time that is 0.9.2.1214.

We are done installing. Move on to [create new peachdocs website]('create') to create a new peachdocs wiki.
