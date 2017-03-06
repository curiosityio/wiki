---
name: Monkey runner
---

# Run monkey runner

Run monkey runner.

```
# runs on emulator (-e) with package (-p com...) 500 times
adb -e shell monkey -p com.packge.name.here -v 500
```

Run monkey runner with seed. Whatever you type in for 'seedHere' will be used as a seed to run the same monkey sequence again.

```
* runs on emulator (-e) with package (-p com...) 500 times
adb -e shell monkey -p com.packge.name.here -v 500 -s seedHere
```
