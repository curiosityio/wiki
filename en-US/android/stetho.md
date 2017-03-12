---
name: Facebook Stetho
---

Facebook has created a tool, [Stetho](https://facebook.github.io/stetho/), you can use to debug your UI, network, read databases. All without need of root or using special software the UI is Chrome dev tools.

Install Stethos into your app, only run on debug builds, and get Realm DB inspection not just SQLite working:

* In root `build.gradle`:

```
allprojects {
    repositories {
        ...
        maven { url 'https://github.com/linchaolong/stetho-realm/raw/master/maven-repo' } <----- add this line.
    }
}
```

This is to install the Realm browsing plugin. Note: I am not using the official DB for the Realm DB browser plugin. The original author's repo is: `https://github.com/uPhyca/stetho-realm/` and you can see above we are using `linchaolong` instead. That is because `linchaolong` created a patch for [this issue](https://github.com/uPhyca/stetho-realm/issues/45) and it has not been merged into master yet for `uPhyca`.

* In `app/build.gradle`, add these dependencies:

```
compile 'com.facebook.stetho:stetho:1.4.2'
compile 'com.facebook.stetho:stetho-okhttp3:1.4.2'
compile 'com.uphyca:stetho_realm:2.0.1'
```

* In your Application code, init Stetho:

```
override fun onCreate() {
    super.onCreate()

    Realm.init(this)

    if (BuildConfig.DEBUG) {
        Stetho.initialize(
                Stetho.newInitializerBuilder(this)
                        .enableDumpapp(Stetho.defaultDumperPluginsProvider(this))
                        .enableWebKitInspector(RealmInspectorModulesProvider.builder(this)
                                .databaseNamePattern(Pattern.compile(".+\\.realm"))
                                .build())
                        .build())
    }
}
```

* In your custom `OkHttpClient`, add a network Interceptor for sniffing the network calls.

```
OkHttpClient client = new OkHttpClient.Builder()
        .addNetworkInterceptor(new Interceptor() {
            @Override
            public Response intercept(Chain chain) throws IOException {
                // do stuff in here for custom Interceptor stuff such as pulling out headers and saving them to shared preferences.
            }
        })
        .addNetworkInterceptor(new StethoInterceptor()) // be sure StethoInterceptor is last. Interceptors above could modify the network and we want to sniff what the app will see.
        .build();
```
