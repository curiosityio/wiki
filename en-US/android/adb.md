---
name: ADB
---

# Run adb shell commands from bash script.

Surround the command you wan to run in quotes to send the commands.

```
adb -d shell "
run-as com.levibostian.foo.debug \
cp /data/data/com.levibostian.foo.debug/files/default.realm /sdcard/
exit
"
adb -d pull /sdcard/default.realm default.realm # <--- this will run as bash, not adb shell command.
```

This will run 3 commands: `run-as`, `cp`, `exit` commands through adb shell on a device (`-d` flag). If you do not use the quotes the shell will start and wait for you to enter input.

If you want to run these commands as root:

```
adb shell "su -c 'run code in these single quotes as root'"
```

# Pull app files from emulator or rooted device.

```
#!/bin/bash

adb -d root
adb -d pull /data/data/com.levibostian.foo.debug/files/default.realm Realm-debug.realm
adb -d unroot
```

Change `com.levibostian.foo.debug` to your namespace for your app.

The file `Realm-debug.realm` in your current directory after running this command will be the file you are looking for.

For emulators, remove the `-d` flag from all the commands. `-d` means device.

# Pull app files from non-rooted device.

```
#!/bin/bash

adb -d shell "
run-as com.levibostian.foo.debug \
cp /data/data/com.levibostian.foo.debug/files/default.realm /sdcard/
exit
"
adb -d pull /sdcard/default.realm default.realm
```

As long as your app is debuggable, this will work. `run-as` is telling adb to run as your app and then it gives you access to the data.
