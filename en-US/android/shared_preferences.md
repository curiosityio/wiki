---
name: Shared Preferences
---

# Delete all shared preferences for app

```
PreferenceManager.getDefaultSharedPreferences(mContext).edit().clear().commit();
```

# Pull shared preferences from non-root phone to read

```
#!/bin/bash

# adb root
# adb pull /data/data/com.curiosityio.foo.debug/files/$1.realm HowFactory-debug.realm
# adb unroot

adb -d shell "
run-as com.curiosityio.foo.debug \
cp /data/data/com.curiosityio.foo.debug/shared_prefs/com.curiosityio.foo.debug_preferences.xml /sdcard/
exit
"
adb -d pull /sdcard/com.curiosityio.foo.debug_preferences.xml /tmp/prefs.xml
```

Make sure to replace `com.curiosityio.foo.debug` with the namespace of your app. You can find this in the app manager on your phone. 
