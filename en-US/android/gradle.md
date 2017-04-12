---
name: Gradle
---

# Make gradle builds faster.

Gradle builds can be slow. Here is how to make them faster.

* Profiling. What is slow? How to benchmark.

```
$> ./gradlew assembleBetaRelease --profile
```

If you run a gradle task with `--profile` argument, this will profile the gradle task. This allows you to find an HTML document in `build/reports/profile/profile-2016-02-02-15-39-17.html` of your project.

![](/docs/images/gradle_run_profile.png)
> View of HTML file example.

* Configure gradle to do less work.

```
$> echo 'org.gradle.configureondemand=true' >> ~/.gradle/gradle.properties
$> echo 'org.gradle.daemon=true' >> ~/.gradle/gradle.properties
$> echo 'org.gradle.parallel=true' >> ~/.gradle/gradle.properties # you may not see much improvement here. Only when you compile many projects at once.
```

* Specify minSdk at compile time. Lots of projects are run with a minSdk of around 16. This could make builds slower because SDK 21+ uses different VM and so the dex process takes longer.

Add this to your `app/build.gradle` file:

```
def minSdk = hasProperty('minSdk') ? minSdk : 16
android {
    compileSdkVersion 24
    buildToolsVersion "24.0.0"

    packagingOptions {
        exclude 'META-INF/ASL2.0'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/NOTICE'
    }

    defaultConfig {
        applicationId "com.foo.bar"
        minSdkVersion minSdk
        targetSdkVersion 24
        versionCode 1
        versionName "0.3.0"
    }
    ...
}
```

Then, when you run the gradle command from command line: `./gradlew assembleBetaRelease -PminSdk=21` it will set minSdk to 21 and will build faster (a good deal faster!). You can go to `Preferences -> Build, Execution, Deployment -> Compiler` in Android Studio and then in the box next to Command-line Options enter `-PminSdk=21` to make Studio run this as well.

![](/docs/images/compiler_minsdk_flag.png)

* Use less dependencies. Use smaller ones. The less code Studio has to compile, the better. Use Picasso instead of Glide for example if you can. Slim down.

* Enable cached builds. This is only enabled on Gradle plugin v2.2+. (It is supposed to be enabled by default in v2.3 or 2.4 but for now, we must enable it ourselves). In your `gradle.properties` files of your project, add this line:

```
android.enableBuildCache=true
```

These tips came from [this great doc](http://www.universalmind.com/blog/10-tips-to-improve-your-gradle-build-time/) and [this one as well.](http://zeroturnaround.com/rebellabs/making-gradle-builds-faster/)

# Find duplicate dependencies in your project

[Thanks to this SO answer for the help with this](http://stackoverflow.com/a/30649660/1486374)

You may need to replace `app` with the name of your module. By default, your main module will be named `app` by Android Studio but you may need to change that.

This command brings up list of all your dependencies and all of their dependencies.  

```
./gradlew -q dependencies app:dependencies --configuration compile
```

Output will resemble something such as this:

```
compile - Classpath for compiling the main sources.
+--- org.androidannotations:androidannotations-api:3.2
+--- com.android.support:support-annotations:22.1.1
+--- com.squareup:otto:1.3.6
+--- in.srain.cube:grid-view-with-header-footer:1.0.10
+--- com.nostra13.universalimageloader:universal-image-loader:1.9.3
+--- com.github.chrisbanes.photoview:library:1.2.3
+--- org.simpleframework:simple-xml:2.7.1
+--- com.google.android.gms:play-services-base:6.5.+ -> 6.5.87
+--- project :yourProject
|    +--- com.loopj.android:android-async-http:1.4.6
|    +--- org.apache.httpcomponents:httpmime:4.2.5
|    |    \--- org.apache.httpcomponents:httpcore:4.2.4
|    \--- com.google.code.gson:gson:2.3.1
+--- project :facebook
|    \--- com.android.support:appcompat-v7:22.1.1
|         \--- com.android.support:support-v4:22.1.1
|              \--- com.android.support:support-annotations:22.1.1 -> 22.2.0
```

Where you see dependencies depending on other dependencies, use this syntax in your build.gradle file:

```
compile('com.github.chrisbanes.photoview:library:1.2.3') {
    exclude group: 'com.android.support'
}
compile('org.simpleframework:simple-xml:2.7.1') {
    exclude module: 'stax'
    exclude module: 'stax-api'
    exclude module: 'xpp3'
}
compile('com.google.android.gms:play-services-base:6.5.+') {
    exclude module: 'support-v4'
}
```

# Install Android library to local Maven repo to install in projects locally.

[Help from](http://stackoverflow.com/a/41997650/1486374)

This is great if you have an Android library you are developing and debugging locally on your machine. You can develop changes in your library, install changes you local Maven repo, install in your Android app by installing the library via Gradle in the Maven local repo.

In your Android library:

* Add plugin to root `build.gradle` file:

```
buildscript {
  repositories {
    mavenCentral() // or if jcenter is already there, ignore this part.
  }

  dependencies {
    classpath 'com.github.dcendents:android-maven-gradle-plugin:1.5'
  }
}
```

* Add the plugin to your librarie's `build.gradle` file:

```
apply plugin: 'com.android.library' // this should already be there
apply plugin: 'com.github.dcendents.android-maven'

group = 'com.yourpackage.yourlibrary'
version = '1.0'
```

* Add to your `settings.gradle` file:

```
rootProject.name = 'yourlibrary'
```

* Then, install the library to the local Maven repo:

```
./gradlew install
```

Now, if you check your local maven repo (usually located: `$HOME/.m2/repository`) you will find your library.

In your Android app project:

* Add the following to your root `build.gradle` file by JitPack and jcenter links:

```
mavenLocal()
```

* Then, in your app's `build.gradle` file:

```
compile 'com.yourpackage.yourlibrary:yourlibrary:1.0'
```

Keep in mind that when you change code in your library project code base, you will always need to run `./gradlew install` to install the changes to the local Maven repo *and then* you will need to do a Gradle sync in Android Studio of your Android app so it can sync the Maven local changes to it's code base. 
