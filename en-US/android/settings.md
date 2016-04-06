---
name: Android settings/preferences
---

If you have a settings screen in your app, super easy to implement.

In this doc:

* [Add settings screen to app](#add-settings-screen-to-app)
* ...anything else you want to do such as creating custom settings, separate screens, lists, etc. check of [Google's doc on settings](https://developer.android.com/guide/topics/ui/settings.html)

# Add settings screen to app

* Create a preferences file for app in XML. Belongs to `res/xml/prefernce_file_name.xml`.
```
<?xml version="1.0" encoding="utf-8"?>
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android">
    <PreferenceCategory
        android:title="@string/notifications">
        <SwitchPreference
            android:title="@string/notifications"
            android:defaultValue="true"
            android:key="@string/notifications_on_off_preference"/>
        <SwitchPreference
            android:title="@string/vibrate"
            android:defaultValue="true"
            android:key="@string/notifications_vibrate_on_off_preference"/>
    </PreferenceCategory>
</PreferenceScreen>
```
`PreferenceCategory` create a group of preferences together with a header text. `SwitchPreference` is a switch widget.

What is cool about preferences with Android, it reads/writes to shared preferences automatically. The `android:key` value that you give is the key that it writes to sharedpreferences for you.

* Set this preferences file in a `PreferenceFragment`.
```
import android.content.SharedPreferences;
import android.os.Bundle;
import android.preference.Preference;
import android.preference.PreferenceFragment;

import javax.inject.Inject;

public class SettingsPreferenceFragment extends PreferenceFragment implements SharedPreferences.OnSharedPreferenceChangeListener {

    private Preference mNotificationsPreference;
    private Preference mNotificationsVibratePreference;

    @Inject SettingsManager mSettingsManager;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        MainApplication.inject(this);

        addPreferencesFromResource(R.xml.settings_preferences);

        mNotificationsPreference = findPreference(getString(R.string.notifications_on_off_preference));
        mNotificationsVibratePreference = findPreference(getString(R.string.notifications_vibrate_on_off_preference));

        setupPreferences();
    }

    private void setupPreferences() {
        enableDisableVibrateDependingOnNotificationsValue();

        getPreferenceManager().getSharedPreferences().registerOnSharedPreferenceChangeListener(this);
    }

    private void enableDisableVibrateDependingOnNotificationsValue() {
        if (mSettingsManager.isNotificationsEnabled()) {
            mNotificationsVibratePreference.setEnabled(true);
        } else {
            mNotificationsVibratePreference.setEnabled(false);
        }
    }

    @Override
    public void onSharedPreferenceChanged(SharedPreferences sharedPreferences, String key) {
        enableDisableVibrateDependingOnNotificationsValue();
    }

}
```
This file creates a fragment that we can display in our app. This example is cool because it takes 2 switch preferences and when notifications switch is disabled, it disables the vibration preference.

* Done. Anywhere in your app that you want to get the value of the "notification" settings now:
```
PreferenceManager.getDefaultSharedPreferences(mContext).getBoolean(mContext.getString(R.string.notifications_on_off_preference), true);
```
