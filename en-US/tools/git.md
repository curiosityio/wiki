---
name: Git
---

# Create git alias commands to make git quicker and easier to work with at command line.

This is a built in feature of git. All you need to do is add some lines to `~/.gitconfig` and git will read the aliases and use them.

Aliases allow you to go from `git init` to `git i` to do the same thing. My favorite is `git ac "Message"` instead of `git add .; git commit -m "Message"`.

```
[alias]
    # one-line log
    l = log --pretty=format:"%C(yellow)%h\\ %ad%Cred%d\\ %Creset%s%Cblue\\ [%cn]" --decorate --date=short

    aa = add .
    a = add
    c = commit --verbose
    ca = commit -a --verbose
    cm = commit -m
    cam = commit -a -m
    cne = commit --amend --no-edit

    d = diff
    ds = diff --stat
    dc = diff --cached

    s = status
    co = checkout
    cob = checkout -b
    # list branches sorted by last modified
    #b = "!git for-each-ref --sort='-authordate' --format='%(authordate)%09%(objectname:short)%09%(refname)' refs/heads | sed -e 's-refs/heads/--'"
    b = branch
    # list aliases
    la = "!git config -l | grep alias | cut -c 7-"

    i = init
    ie = "!git init && git config user.email"
    rao = remote add origin
    p = push
    po = push -u origin
    pf = push -f
    ac = "!f() { git add .; echo $1; git commit -m \"$1\"; bash-reminder \"**Do some sketches for your next task**!\"; }; f"
    pushall = push -u origin --all
```

Your `.gitconfig` file might have more in it, but the [alias] part is what we will be adding. This is a copy of my config file with the aliases I have in there.

*Advanced:*

For one of these commands, I am doing something a little more advanced. I am running bash along with my git alias. Let's say you have a git alias you want to create and run some random bash command along with the git alias you are creating. For example, I have this bash cli program, [bash-reminder](https://github.com/levibostian/bash-reminder), that I want to run each time I run `git ac` alias command. So, to do that, I add the following alias to my `~/.gitconfig` file:

```
ac = "!f() { git add .; echo $1; git commit -m \"$1\"; bash-reminder \"**Do some sketches for your next task**!\"; }; f"
```

The secret here is the `!` at the beginning. This means to run bash. Anything you put after the `!` is bash. Also, because I am running bash commands after my git commands, I need to manually send command line parameters to my alias. As you can see in the above line, I have `git commit -am $1;`. The $1 will send whatever message I send to the git alias command there.
