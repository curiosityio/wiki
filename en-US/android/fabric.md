---
name: Fabric
---

# Initialize in Application

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
    if (BuildUtil.isDebug()) {
        e.printStackTrace()
    } else {
        Crashlytics.getInstance().core.logException(e)
    }
}
```

Could make this a Kotlin extension for Log too.
