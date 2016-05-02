---
name: Broadcast receivers
---

# Do something when WiFi state changes

1. manifest:
```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.levibostian.whatever">

    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

    <application>
        ...
        <activity>
            ...
        </activity>

        <receiver android:name=".WiFiStateReceiver">
            <intent-filter android:priority="100">
                <action android:name="android.net.wifi.STATE_CHANGE" />
            </intent-filter>
        </receiver>

    </application>

</manifest>
```
Change the `android:name=""` part to point to the path of your receiver class. `.WiFiStateReceiver` is assuming your path is: `app/src/main/java/com/levibostian/whatever/WiFiStateReceiver` but you can put it inside of a package of course. `.packagename.WiFiStateReceiver`.

2. Create your broadcast receiver class:
```
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.net.NetworkInfo;
import android.net.wifi.WifiInfo;
import android.net.wifi.WifiManager;

public class WiFiStateReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        NetworkInfo info = intent.getParcelableExtra(WifiManager.EXTRA_NETWORK_INFO);
        if (info != null && info.isConnected()) {
            startApp(context);

            // in case you care, you can get wifi network info here such as SSID.
            WifiManager wifiManager = (WifiManager)context.getSystemService(Context.WIFI_SERVICE);
            WifiInfo wifiInfo = wifiManager.getConnectionInfo();
            String ssid = wifiInfo.getSSID();
        }
    }

    private void startApp(Context context) {
        Intent serviceIntent = new Intent(context, MainActivity.class);
        serviceIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);

        context.startActivity(serviceIntent);
    }

}
```
This example will launch your app when the WiFi is connected. You can do something else however.

# Launch app activity on system boot

*Note: Android N has a new service called Direct Boot. RECEIVE_BOOT_COMPLETED will run after the device boots and then the user unlocks the device for the first time. If you need your app to start on boot before unlock, check out [this blog post](http://android-developers.blogspot.com/2016/04/developing-for-direct-boot.html)*

Keep in mind that if you have an app that requires network connection, launching on system boot may not work best for you. Launch the activity instead when the wifi state becomes connected.

You don't have to start an activity on system boot. You can launch a service or something as well.

1. manfiest:
```
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

 <application
 ...
    <receiver
        android:name=".StartMyActivityAtBootReceiver"
        android:enabled="true"
        android:permission="android.permission.RECEIVE_BOOT_COMPLETED" >
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />

            <category android:name="android.intent.category.DEFAULT" />
        </intent-filter>
    </receiver>
 </application>
```

2. Create your Broadcast receiver:
```
public class StartMyActivityAtBootReceiver extends BroadcastReceiver {

    @Override
    public void onReceive(Context context, Intent intent) {
        if (intent.getAction().equals(Intent.ACTION_BOOT_COMPLETED)) {
            startApp(context);
        }
    }

    private void startApp(Context context) {
        Intent serviceIntent = new Intent(context, MainActivity.class);
        serviceIntent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);

        context.startActivity(serviceIntent);
    }

}
```
