---
name: Android push notifications
---

I use Amazon SNS to send push notifications. It is nice because in order to send push notifications, you have to use Google's GCM to talk to Android apps, and you have to use ADM for Apple messaging with iOS devices (and many others to talk to desktop and Windows devices). Instead of making your server work with each of these services individually, Amazon SNS communicates to them for you.

AWS SNS is cheap. First 1 million each month is free. Then it's $.50 for each million after that. You can make your own server to do this for you, but for that price it might be worth letting AWS take care of some of the heavy lifting for you.

# Get push notifications for Android

1. [Register your app with Google Cloud Messaging](https://developers.google.com/mobile/add). Follow the directions on the screen. If you have already created a Google cloud platform project for this app then use the dropdown to select the project. For package name, enter your app package name for production (ignore flavors extensions). When asked what services to install, enable GCM.

In the final step after you enable GCM services, you will be given the option to download a `google-services.json` file.
Download this file into your `/app` directory *if* you only have one flavor. If you have many flavors (I usually have a development, beta, and production flavor) then download the `google-services.json` file to your `/app/src/flavor` directory. Copy this exact same file and put it into all of your flavor directories so in my case with production, beta and development, this is my structure:
```
/app/src
    /production/
        google-services.json
    /beta/
        google-services.json
    /development/
        google-services.json
```
Then, you will need to go into your beta and development `google-services.json` files. Leave the file exactly the same as the production file, however there is one thing you need to change:
```
{
  "project_info": {
    "project_id": "project-id-here",
    "project_number": "555555555555",
    "name": "App name here"
  },
  "client": [
    {
      "client_info": {
        "mobilesdk_app_id": "1:34343434:android:343434343434",
        "client_id": "android:com.example.app",
        "client_type": 1,
        "android_client_info": {
          "package_name": "com.example.app.debug", <------ these needs to be changed to your app flavor's package name. This one is for the development flavor.
          "certificate_hash": []
        }
        ...
```

2. Install dependencies in Android app via Gradle.

In your `/build.gradle` file add a new classpath for GMS.
```
buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        ...
        classpath 'com.google.gms:google-services:2.0.0'

        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}
...
```
[Check latest version of plugin](https://bintray.com/android/android-tools/com.google.gms.google-services/).

In your `/app/build.gradle` file add dependencies and add a plugin. The plugin is parse the google-services file and the dependency is for the GCM library.
```
apply plugin: 'com.android.application'
...

android {
    ...
}

dependencies {
    ...
    compile 'com.google.android.gms:play-services-gcm:8.4.0'
    ...
}

apply plugin: 'com.google.gms.google-services'
```
No, you are not seeing that incorrectly. The `apply plugin` line is at the bottom of the file.

3. Edit your manifest:
```
<manifest package="your.package.name" ...>

    <permission android:name="<your-package-name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="<your-package-name>.permission.C2D_MESSAGE" />

    <application ...>
        <receiver
            android:name="com.google.android.gms.gcm.GcmReceiver"
            android:exported="true"
            android:permission="com.google.android.c2dm.permission.SEND" >
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="com.example.gcm" />
            </intent-filter>
        </receiver>
        <service
            android:name="com.example.ApplicationNameHereGcmListenerService"
            android:exported="false" >
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            </intent-filter>
        </service>
        <service
            android:name="com.example.ApplicationNameHereGcmInstanceIDListenerService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.android.gms.iid.InstanceID" />
            </intent-filter>
        </service>
        <service
            android:name="com.example.ApplicationNameHereGCMRegistrationIntentService"
            android:exported="false">
        </service>
    </application>

</manifest>
```
Replace `<your-package-name>` with your package name.

You will notice 3 files missing. `com.example.ApplicationNameHereGcmListenerService`, `com.example.ApplicationNameHereGCMRegistrationIntentService` and `com.example.ApplicationNameHereGcmInstanceIDListenerService`. Lets create those and not name them like that.

4. Create some service files.

* Create a service that is designed to get a GCM registration token and send it up to your server eventually:
```
import android.app.IntentService;
import android.content.Intent;
import android.util.Log;
import com.google.android.gms.gcm.GoogleCloudMessaging;
import com.google.android.gms.iid.InstanceID;

import java.io.IOException;

public class ApplicationNameHereGCMRegistrationIntentService extends IntentService {

    private static final String TAG = "ApplicationNameHereGCMRegistrationIntentService";

    public ApplicationNameHereGCMRegistrationIntentService() {
        super(TAG);
    }

    @Override
    protected void onHandleIntent(Intent intent) {
        InstanceID instanceID = InstanceID.getInstance(this);

        try {
            String gcmToken = instanceID.getToken(getString(R.string.gcm_sender_id), GoogleCloudMessaging.INSTANCE_ID_SCOPE, null);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

}
```
Replace `ApplicationNameHere` with your application name.

You need to also create a `gcm_sender_id` string in your string resource file. The GCM sender ID is your Google Cloud Platform Project name. Open the `google-services.json` file to find it or go to the [GCM dashboard](https://console.cloud.google.com/home/dashboard) to find your project *name* (not ID) of each project (there is a little drop down by project ID you can click to show the project name).

* Create a `InstanceIDListenerService` file. This file handles when GCM gives you a refreshed token and you need to send that new token to your server. So we simply tell our service we created in the previous step to start to do all that for us.
```
public class ApplicationNameHereGCMInstanceIdListenerService extends InstanceIDListenerService {

    @Override
    public void onTokenRefresh() {
        Intent intent = new Intent(this, ApplicationNameHereGCMRegistrationIntentService.class);
        startService(intent);
    }

}
```
Replace `ApplicationNameHere` with your application name.

* Create `GcmListenerService` file:
```
public class ApplicationNameHereGCMListenerService extends GcmListenerService {
}
```
Replace `ApplicationNameHere` with your application name.

5. [Check Google Play Services is installed in app](./google_play_services#check-installed-on-android-app)

6. Obtain a GCM registration token from GCM.

In your activity where you want to register for a GCM token:
```
@Override
protected void onResume() {
    super.onResume();

    if (checkPlayServicesInstalled()) {
        registerAppWithGCM();
    }
}

private void registerAppWithGCM() {
    startService(IntentUtil.startActivityIntent(this, ApplicationNameHereGCMRegistrationIntentService.class));
}
```
