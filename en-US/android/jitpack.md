---
name: Release Android library to JitPack.io
---

JitPack is like Maven Central, but better. Some remote package managers require lots of setup to get working. JitPack is extremely easy where all it takes is a Git repo.

# Release Android library project to JitPack

Do the following:

* Create an Android library project.
* Push up to a GitHub public repo (you can use private too but it costs)
* Create a release using git tags:

```
git tag 1.0 # or whatever version you want to release
git push
git push --tags
```

Done. Now you can include your library project in an Android app:

```
repositories {
	maven { url = 'https://jitpack.io' }
}
dependencies {
	// for https://github.com/user/library with tag 1.0:
	compile 'com.github.user:library:1.0'
}
```

*Tip:* If you don’t have an assigned tag yet - you may use `com.github.user:library:-SNAPSHOT` and will always get the “tip” of the master branch.

[Here](https://jitpack.io/docs/ANDROID/) are the official docs on publishing an Android library with JitPack however, I feel they make it sound much more confusing. Especially because they say it requires using a maven plugin but I have found that isn't the case? (or at least that isn't the case with [this repo](https://github.com/thorbenprimke/realm-recyclerview) or [my fork](https://github.com/curiosityio/Realm-RecyclerView) of it)

[Official JitPack android example skeleton repo](https://github.com/jitpack/android-example/)
[Official JitPack android library with multiple product flavors example skeleton repo](https://github.com/jitpack-io/android-jitpack-library-example)

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
