---
name: Dagger
---

# Dagger 2 with Kotlin

* build.gradle

```
apply plugin: 'com.android.application'
apply plugin: 'kotlin-android'
apply plugin: 'kotlin-kapt' // <----- important. If you have a project that is 100% kotlin, remove android-apt plugin and use kotlin instead.

repositories {
    mavenCentral()
}

android {
    ...    
}

// Notice I am *not* using `kapt { generate stubs true }`` here. I followed [this project](https://github.com/damianpetla/kotlin-dagger-example) and copied his build.gradle file. After that, got it all working.
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    testCompile 'junit:junit:4.12'

    kapt 'com.google.dagger:dagger-compiler:2.8' // dependency.
    compile 'com.google.dagger:dagger:2.8' // dependency.

    compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
}
```
* Create file `ApplicationComponent` and put in it:

```
package com.levibostian.androidblanky

import com.levibostian.androidblanky.fragment.MainFragment
import com.levibostian.androidblanky.module.ApiModule
import com.levibostian.androidblanky.module.ManagerModule
import dagger.Component
import javax.inject.Singleton

@Singleton
@Component(modules = arrayOf(ApiModule::class, ManagerModule::class))
interface ApplicationComponent {
    fun inject(mainFragment: MainFragment)
}
```

* MainApplication

```
package com.levibostian.androidblanky

import android.app.Application
import com.levibostian.androidblanky.module.ApiModule
import com.levibostian.androidblanky.module.ManagerModule

import javax.inject.Singleton

class MainApplication : Application() {

    companion object {
        @JvmStatic lateinit var component: ApplicationComponent
    }

    override fun onCreate() {
        super.onCreate()

        component = DaggerApplicationComponent.builder()
                .apiModule(ApiModule(this))
                .managerModule(ManagerModule(this))
                .build()
    }    

}
```

Don't forget to set application in manifest file. 
