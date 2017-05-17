---
name: Cleanup
---

Sometimes XCode starts to get a little slow and takes up many GBs of data. Time to clean it up.

# Clean up files

You can go into these directories below and delete all files inside of them. Do not delete the actual directory! Delete the files inside:

* `~/Library/Developer/Xcode/DerivedData`
* `~/Library/Developer/Xcode/Archives`

These directories you can delete everything *but* the newest version. So look inside of these directories and keep the directory that is the newest, delete others:

* `~/Library/Developer/Xcode/iOS DeviceSupport`

# Clean up simulators

When new iOS versions come out, you will probably start compiling your apps to those newer versions of iOS. But, XCode keeps all simulators for you no matter what version of iOS. So, time to time it's best to go in and clean them up to only keep 1 simulator of each type of each iOS version.

Fastlane has created [a simple method](https://github.com/fastlane/fastlane/tree/master/snapshot#completely-reset-all-simulators) that deletes all of your old simulators and recreates them.

```
fastlane snapshot reset_simulators
```
