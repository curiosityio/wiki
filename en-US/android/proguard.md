---
name: ProGuard
---

[Official doc](https://developer.android.com/studio/build/shrink-code.html)

# Install ProGuard into app

* In your `app/build.gradle` file, add 2 lines of code to your release build type:

```
android {
    ...
    buildTypes {
        release {
            shrinkResources true // removes resources app not using in app.
            minifyEnabled true // enables proguard
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'

            signingConfig signingConfigs.releaseSigning
        }
    }
    ...
}
```

* Create or open file `app/proguard-rules.pro`. This is where you will be adding ProGuard specific rules for your app.

```
# Add project specific ProGuard rules here.
# By default, the flags in this file are appended to flags specified
# in /Users/levibostian/Library/Android/sdk/tools/proguard/proguard-android.txt
# You can edit the include path and order by changing the proguardFiles
# directive in build.gradle.
#
# For more details, see
#   http://developer.android.com/guide/developing/tools/proguard.html

# Add any project specific keep options here:

# If your project uses WebView with JS, uncomment the following
# and specify the fully qualified class name to the JavaScript interface
# class:
#-keepclassmembers class fqcn.of.javascript.interface.for.webview {
#   public *;
#}

# Keep VO and Model class variables for Realm and Retrofit/Jackson
-keep public class com.foo.foo.db.models.** {
  public protected *;
  public void set*(***);
  public *** get*();
}
-keep public class com.foo.foo.vo.** {
  public protected *;
  public void set*(***);
  public *** get*();
}

# Jackson
-keepattributes *Annotation*,EnclosingMethod,Signature
-keepnames class com.fasterxml.jackson.** { *; }
-dontwarn com.fasterxml.jackson.databind.**
-keep class org.codehaus.** { *; }
-keepclassmembers public final enum org.codehaus.jackson.annotate.JsonAutoDetect$Visibility {
public static final org.codehaus.jackson.annotate.JsonAutoDetect$Visibility *; }
-keep class com.fasterxml.jackson.databind.ObjectMapper {
    public <methods>;
    protected <methods>;
}
-keep class com.fasterxml.jackson.databind.ObjectWriter {
    public ** writeValueAsString(**);
}

# Retrofit
-dontwarn okio.**
-dontwarn retrofit2.Platform$Java8

# RxJava
-dontwarn sun.misc.**
-keepclassmembers class rx.internal.util.unsafe.*ArrayQueue*Field* {
   long producerIndex;
   long consumerIndex;
}
-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueProducerNodeRef {
    rx.internal.util.atomic.LinkedQueueNode producerNode;
}
-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueConsumerNodeRef {
    rx.internal.util.atomic.LinkedQueueNode consumerNode;
}

# EventBus
-keepattributes *Annotation*
-keepclassmembers class ** {
    @org.greenrobot.eventbus.Subscribe <methods>;
}
-keep enum org.greenrobot.eventbus.ThreadMode { *; }
# Only required if you use AsyncExecutor
-keepclassmembers class * extends org.greenrobot.eventbus.util.ThrowableFailureEvent {
    <init>(java.lang.Throwable);
}

# Fabric
-keepattributes *Annotation*
-keepattributes SourceFile,LineNumberTable
```

These are some common rules for some common libraries. [Here](https://github.com/krschultz/android-proguard-snippets) is a GitHub project with library rules community created which might help. Start out with the bare minimum number of rules and try to compile your app inside of terminal (Studio works sometimes but if you run into issues, I have frozen with Studio before). `./gradlew assembleRelease` will compile with ProGuard. Watch the output of the terminal. If the compile throws an error, view the gradle output. You will see where ProGuard starts. The output will show lines starting with "Note: ". These can be ignored. It's the lines that start with "Warning: " that stops compiling. Google these and it will help find solutions for you.

* Be sure to run the app on a device after running ProGuard to test if it works. ProGuard can remove needed code from your project and kill your app.
