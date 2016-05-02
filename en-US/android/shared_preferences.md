---
name: Shared Preferences
---

# Delete all shared preferences for app

```
mSharedPreferences = PreferenceManager.getDefaultSharedPreferences(mContext);

mSharedPreferences.edit().clear().commit();
```
