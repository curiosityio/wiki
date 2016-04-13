---
name: Lock orientation Android.
---

Sometimes you want to lock the orientation to landscape or portrait for an app.

You have two options:

1. Activity by activity basis in the manifest:
```
<activity android:name="MyActivity"
  android:screenOrientation="landscape"
  android:configChanges="keyboardHidden|orientation|screenSize">
...
</activity>
```
Where MyActivity is the one you want to stay in landscape.

The android:configChanges=... line prevents onResume(), onPause() from being called when the screen is rotated. Without this line, the rotation will stay as you requested but the calls will still be made.

Note: keyboardHidden and orientation are required for < Android 3.2 (API level 13), and all three options are required 3.2 or above, not just orientation.

2. Activity by activity basis in the activity code:
```
@Override
protected void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);
}
```

So if you want to set it for the entire app, I use the code approach by creating a `BaseActivity` and set the orientation in the `onCreate()`. Then have all of your activities inherit the base activity.
