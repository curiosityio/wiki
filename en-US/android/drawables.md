---
name: Drawables
---

# Examples

* Grey rectangle
```
<?xml version="1.0" encoding="UTF-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
    <solid android:color="#CCCCCC"/>
</shape>
```

You can now add a border:
```
<?xml version="1.0" encoding="UTF-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
    <solid android:color="#CCCCCC"/>
    <stroke android:width="1px" android:color="@color/blue"/>
</shape>
```

You can add some rounded corners:
```
<?xml version="1.0" encoding="UTF-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
    <solid android:color="#CCCCCC"/>
    <stroke android:width="1px" android:color="@color/blue"/>
    <corners android:radius="20dp"/>
</shape>
```

You can add some padding to the border:
```
<?xml version="1.0" encoding="UTF-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
    <solid android:color="#CCCCCC"/>
    <stroke android:width="1px" android:color="@color/blue"/>
    <padding android:left="10dp" android:right="10dp" android:top="10dp" android:bottom="10dp"/>
    <corners android:radius="20dp"/>
</shape>
```

# Use vector drawables (aka SVG images) within app.

[Help from Android blog post](https://android-developers.blogspot.com/2016/02/android-support-library-232.html)

Vector drawables replace the need to generate png images for image resources. This saves space and improves speed.

If you want to use Vector Drawables with an app pre Android 5.0, you must use the AppCompat support library.

To get vector drawables working in your app:

* Add AppCompat support lib to gradle.

* In your build.gradle file, add this setting (this works for gradle build versions 2+):

```
android {
    defaultConfig {
        ...

        vectorDrawables.useSupportLibrary = true
    }
    ...
}
```

This tells Studio to use vector drawables instead of have Studio generate pngs at buildtime.

* In your ImageViews, ImageButton, places in your XML where you use `android:src="@drawable/foo"` to set a drawable image, you must now use: `app:srcCompat="@drawable/foo_vector"`.

* That is it. AppCompat takes care of rest. All functions such as `.setImageResource()` on ImageView works as normal.

When you want to use SVG or PSD files in your Android app, you need to use the [Android Asset Studio tool](https://developer.android.com/studio/write/vector-asset-studio.html). Access tool by right click on `res` in Android Studio > New > Vector asset > Local file > browse for your SVG or PSD file > Next. This will generate a XML file from these files as you cannot use SVG or PSD files directly in Studio.

# Background pressed state for views

When you press on any type of View that is not a button, you want it to have some sort View change to tell the user what they pressed on is an action.

* Create drawable defining the states:

```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_pressed="true" android:drawable="@color/button_pressed_background_color" />
    <item android:state_focused="true" android:drawable="@color/button_pressed_background_color" />
    <item android:drawable="@android:color/transparent" />
</selector>
```

* Set the background drawable to the View you want to apply this pressed animation to.

```
<LinearLayout
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="horizontal"
    android:background="@drawable/pressed_state_background"> <-------
    ...
</LinearLayout>
```
