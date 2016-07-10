---
name: Android Studio
---

# Trigger breakpoint on all code exceptions.

Like in XCode, it is nice for the IDE to automatically suspend on any exception thrown in your app to debug on the spot.

How to do it:

* Open the Breakpoints window via `Run -> View Breakpoints.`
* Check `Java Exception Breakpoints` checkbox on the left side and `Any exception`.
* Select the `Any exception` row to bring up more options.

Make sure screen looks like the below image (for now, ignore the class filters. That is the next step):

![](/docs/images/android_studio_breakpoint_configure.png)

* Check the `class filters` checkbox the on the right side, click the 3 dots button.
* In the popup, click the `Add pattern` button (button with \*? on it) to add a new regex pattern and enter `com.whatever.*` (your app's namespace)

[Original doc](http://stackoverflow.com/a/28862538/1486374)

# Use system clipboard

Android Studio by default (on Mac at least) doesn't use the system clipboard. If you use a clipboard history tool, it may not work with Studio (well, IntelliJ really)

Do the following to get it Studio to use the system clipboard:

* Browse to `~/Library/Preferences/{FOLDER_NAME}/` where FOLDER_NAME is `AndroidStudioX.X/` (version of studio you are using) [this doc](https://sites.google.com/a/android.com/tools/tech-docs/configuration) will give you a table of what the folder name should be.
* Create a new file: `idea.properties` in this directory.
* Add the following to this file: `ide.mac.useNativeClipboard=True` save and exit.
* Make sure permissions of idea.properties file are readable. The permissions I have setup and working: `-rw-r--r-- 1 levibostian  staff idea.properties`
* Restart Studio.

Solution came from [here](http://stackoverflow.com/a/27951365/1486374) with help on where to create the idea.properties file from [here](https://sites.google.com/a/android.com/tools/tech-docs/configuration)
