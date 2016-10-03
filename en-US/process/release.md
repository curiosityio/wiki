---
name: Release process
---

# Android

* Set version in `app/build.gradle` file:

```
buildscript {
    ...
}
apply plugin: 'com.android.application'

android {
    ...

    defaultConfig {
        versionCode 1        // <-- leave version code way it is.
        versionName "0.1.0"  // <-- set version code for app. Start releases with v0.1.0 and use semantic version numbering.
    }

    ...
}

dependencies {
    ...
}
```

* Git tag

```
$> git tag -a v0.1.0
```

Your text editor will popup. Enter release notes into the editor that comes up. Save file, exit editor, git will create tag.

Then push the tag to remote repo:

```
$> git push --tags
```

* Create release gradle build:

```
$> ./gradlew assembleBeta
```

This will create APKs for *all* Beta build variants. You can take a shortcut and create for specific variants:

```
$> ./gradlew assembleBetaRelease
```

* Browse in Finder to `app/build/outputs/apk/` and you will find your built APKs. Send up to Fabric.io Beta for distribution. 
