---
name: Install nodejs
---

Install nodejs via apt-get `sudo apt-get update; sudo apt-get install -y nodejs; sudo apt-get install -y npm;`

After you're done installing, the command `nodejs` (/usr/bin/nodejs) should be available. But, the command `node` is important to have as programs use that. They don't call `nodejs` command, they just say `node`. So I make a system link: `ln -s /usr/bin/nodejs /usr/bin/node`
