---
name: Launch app from website URL
---

Launch your app when someone visits your website instead of opening web page on browser.

So do the following in your app, open up http://curiosityio.com from email, browser, anywhere and your app will launch instead of the web browser. If your app is not installed on the device, then the browser will pop up which indicates that the app is not installed so you can display a HTML page here to the play store.

**Note: All info obtained from this [official google doc](https://developer.android.com/training/app-links/index.html)**

How to get it working:

* AndroidManifest.xml:  

```xml
        ...
        <activity android:name=".YourActivity">
           ...            
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="http" android:host="curiosityio.com"/> <!-- here, you can add more such as android:path="" or android:pathPrefix="" to open only certain paths. -->
            </intent-filter>
            ...
        </activity>
        ...
```
(The intent filter can be located in the same intent filter as the activity set to launch in the app, or you can use a separate activity. Either works)

This intent filter is what tells Android your activity will open up links to http://curiosityio.com. Here, we are stating we open http://curiosityio.com and that is it. We do not open https://curiosityio.com, www.curiosityio.com, none of those. We need to create multiple <data .../> entries to do that. Create one <data .../> for each different one (one for https, one for www, etc.)

Also, keep in mind here that we are accepting *all* paths for curiosityio.com. If you only want to catch curiosityio.com/about-us and nothing else, add android:pathPrefix="/path/goes/here" or android:path="/path/goes/here/" to the <data .../> do catch it.

Other things you can add to this intent filter:  
android:launchMode="singleTask". If this is not set, you run the chances of having two instances of your app running if another app launches your app via the custom scheme like we want to do here and if the user manually launches the app later. If you are just showing off a map or sending a tweet, having two instances if not a big deal but other times it may be a big deal especially with using MainActivity that does a lot.

android:alwaysRetainTaskState="true". If user launches your app, leaves the app, then opens your app later on manually or from the custom scheme, the state of the app is retained.

* In your activity that is launched from the intent:  
```java
        final Intent intent = getIntent();
        final String action = intent.getAction();

        // below is how you get the path from the intent filter. so if http://curiosityio.com/about launched this activity, then segments.get(1) should return back "about"....I think....(it might actually return ?user=101 or something. I am not sure.
        if (Intent.ACTION_VIEW.equals(action)) {
            final List<String> segments = intent.getData().getPathSegments();
            if (segments.size() > 1) {
                //mUsername = segments.get(1);
            }
        }
```
This is how you get info on the intent on what URL the user used to get to this app/activity.

* Cool. Now time to test. Install the app on your device and use this adb command to test that your app launches:  

```
adb shell am start -a android.intent.action.VIEW -c android.intent.category.BROWSABLE -d "http://curiosityio.com"
```

Enter whatever URL you want to test. If your app is shown in the dialog to the user asking what app to launch, you have success.

* You are really done now to this point. However, there is something optional that you can also do. Right now, when you launch the intent, the user is approached with many different apps they can launch (browsers mostly come up). If you own this domain and only want your app to launch bypassing this dialog, you can do so. [keep reading to view how](#request-app-link-verification)


# Request App Links Verification

Instead of having a dialog pop up asking the user if they want to launch your app via browser, email, or your app, etc. you can force the device to open your app skipping all of that.

*do before you continue:*

* Have a domain name and web server set up for this domain name.
* Install SSL cert for domain name as Verification is done through HTTPS only.

[Official docs](https://developer.android.com/training/app-links/index.html#request-verify)
Check out the official docs if you have many apps, subdomains, many domains, etc to verify all at once.

* In your manifest where you created your intent-filter, add `android:autoVerify="true"`:

```
<activity ...>

    <intent-filter android:autoVerify="true">
        <action android:name="android.intent.action.VIEW" /> <!-- required -->
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" /> <!-- required -->
        <data android:scheme="https" android:host="curiosityio.com"/> <!-- you MUST use HTTPS to autoVerify. You can include others too, but https must be one declared. -->
    </intent-filter>

</activity>
```

* Upload a file to your web server to verify you own domain and app:

```
[{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "com.example",
    "sha256_cert_fingerprints":
    ["14:6D:E9:83:C5:73:06:50:D8:EE:B9:95:2F:34:FC:64:16:A0:83:42:E6:1D:BE:A8:8A:04:96:B2:3F:CF:44:E5"]
  }
}]
```

Replace `package_name` with the package name in your android manifest.
Replace `sha256_cert_fingerprints` with your app keystore file's sha256 cert fingerprint. To obtain this, you must (1) create a .keystore file for app (keystore files are used to release app to play store) and (2) run this command to get fingerprint:

```
cd /path/to/app/keystore/file
keytool -list -v -keystore my-release-key.keystore
```

Replace `my-release-key` with your keystore file name. You will be asked for a password. It is your store password used when you created the password.

* After file is uploaded to your `https://website.com/.well-known/assetlinks.json` test to see if works.

Open your browser and go to:

```
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=https://<domain1>&relation=delegate_permission/common.handle_all_urls
```

Replace <domain1> with your domain. If you need to add a port number to your domain, use this command instead:

```
https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=https://<domain1>:<port>&relation=delegate_permission/common.handle_all_urls
```

You should see the contents of your file and then on the bottom of screen, a message something like:

```
********************* ERRORS ********************* None! ********************* INFO MESSAGES ********************* ...
...
```

* Install your app on your device. Wait 20 seconds max to allow phone to verify app. Then test with:
*Note: if you have many build flavors, it will only work for the production app as that is the package name you have in your assetlinks.json file.*

```
adb shell am start -a android.intent.action.VIEW -c android.intent.category.BROWSABLE -d "https://curiosityio.com"
```

Or whatever path you have in your manifest and your app should launch right away without dialog!
