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

These tips came from [this great doc](http://www.universalmind.com/blog/10-tips-to-improve-your-gradle-build-time/) and [this one as well.](http://zeroturnaround.com/rebellabs/making-gradle-builds-faster/)
