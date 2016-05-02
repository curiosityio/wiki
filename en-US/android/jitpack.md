---
name: Release Android library to JitPack.io
---

JitPack is like Maven Central, but better. Some remote package managers require lots of setup to get working. JitPack is extremely easy where all it takes is a Git repo.

#### I just created a new git tag release and JitPack made a build for my library but I have a bug and want to fix it.

It is best if you can test for your bugs before you create a git tag release and JitPack creates a build, but there is a way to fix it.

* Fix your library bugs, commit changes on git.
* Delete your broken version git tag: `git tag -d 0.9.16` and delete it remotely: `git push origin :refs/tags/0.9.16`.
* Create a new git tag: `git tag 0.9.16`. (I am using the same tag as I deleted previously so we don't have a version 0.9.16 that is broken for everyone that tries to download it).
* Push tag up to remote: `git push --tags`.

This is easy, you know this. The problem is now that all projects that previously downloaded this version to their machines have a cached copy of the broken library. *AND* JitPack does not rebuild tags automatically. Once you push up a tag, it builds it and if you delete and repush a tag, it will just issue the broken version to everyone else. So we need to delete our caches on your machines that include the broken library and rebuild using JitPack.

* Tell JitPack to rebuld project:

  * Go to [JitPack website](https://jitpack.io/) and sign in with GitHub in the top right corner.
  * Paste in the GitHub URL of your library project in the "Look up" textbox. click "Look up".
  * You will see a list of all tags with buttons on the right that say "Get it". On the right hand side, you can click on a X icon. This deletes the current broken build.

* Delete caches of broken library on machine:

```
rm -rf ~/.gradle/caches/

cd /your/android/studio/project/root/

rm -rf build/
rm -rf app/build/
```

Now when you do a gradle sync, JitPack will be triggered to download a new version of the library and you will download the newest version to your machine. 
