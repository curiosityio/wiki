---
name: Emulator
---

# Charles proxy with emulator

* Find the avd name of the emulator you want to start:

```
$> ~/Library/Android/sdk/tools/android list avd
```

For the example here, let's say my avd name happens to be 'Nexus_5_API_25'.

* Get IP address of your laptop:

```
$> ifconfig
```

Look for the `inet` value under `eth0`.

For this example, let's say mine is `192.168.1.8`.

* Start emulator via command-line enabling proxy mode:

```
$> ./Library/Android/sdk/tools/emulator -avd Nexus_5_API_25 -http-proxy http://192.168.1.8:8888
```

* In Charles > Help > SSL > Install Charles SSL Certificate on a Mobile Device.

A popup will show up telling you how to install. Follow those instructions.

At first, you may have problems with SSL on the emulator connecting to your APIs/websites. Restart emulator, Charles, and possibly reboot machine to get working.

# Better performance emulator

Besides gpu acceleration, Intel HAWX acceleration installed via SDK tools, disabling sound on your emulator makes a big difference to performance.

```
$> nano ~/.android/avd/<image>/config.ini
```

Edit this line: `hw.audioInput=yes` to `hw.audioInput=no`. Then, below that line, add `hw.audioOutput=no`. 
