---
name: Fabric.io
---

# Install Fabric.io into Android app

* Download Android Studio Fabric.io plugin. Login, select `Create new app` and follow steps to install Crashlytics into your Android project.

* Create or add to your class LogUtil to add a convenient way of logging crashes in your app.

```

fun error(e: Throwable) {
    if (BuildUtil.isDebug()) {
        e.printStackTrace()
    } else {
        Crashlytics.getInstance().core.logException(e)
    }
}
```

Here, if you are doing debugging, it will print to console, else it will record to Fabric.

* In your custom Application file, initialize Fabric:

```
@Override
public void onCreate() {
    super.onCreate();

    CrashlyticsCore core = new CrashlyticsCore.Builder().disabled(BuildConfig.DEBUG).build();
    final Fabric fabric = new Fabric.Builder(this)
                                  .kits(new Crashlytics.Builder().core(core).build())
                                  .debuggable(true)
                                  .build();
    Fabric.with(fabric);
}
```

Do this to disable Crashlytics on debugging but send up reports otherwise. 
