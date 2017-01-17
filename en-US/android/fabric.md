---
name: Fabric
---

# Install

* Add to `app/build.gradle`:

```
buildscript {
    repositories {
        maven { url 'https://maven.fabric.io/public' }
        mavenCentral()
    }

    dependencies {
        classpath 'io.fabric.tools:gradle:1.+'
    }
}
apply plugin: 'com.android.application'
apply plugin: 'io.fabric'

...

repositories {
    maven { url 'https://maven.fabric.io/public' }
    mavenCentral()
}

android {
    ...
}

dependencies {
    ...
    compile('com.crashlytics.sdk.android:crashlytics:2.6.5@aar') {
        transitive = true;
    }
}

```

* Add to your manifest:

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.levibostian.foo">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:name="com.levibostian.foo.MainApplication"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>        
        <meta-data
            android:name="io.fabric.ApiKey"
            android:value="keygoesherethatfabricgivesyou" />
    </application>

</manifest>

```

* Add to your Application file:

```
class MainApplication : Application() {

    override fun onCreate() {
        super.onCreate()

        val core = CrashlyticsCore.Builder().disabled(BuildConfig.DEBUG).build()
        val fabric = Fabric.Builder(this).kits(Crashlytics.Builder().core(core).build()).debuggable(true).build()
        Fabric.with(fabric)
    }
}
```

# Util for logging errors and sending up to Crashlytics

```
fun error(e: Throwable) {
    if (BuildConfig.DEBUG) {
        e.printStackTrace()
    } else {
        Crashlytics.getInstance().core.logException(e)
    }
}
```

Could make this a Kotlin extension for Log too.
