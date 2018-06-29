---
name: Intent
---

# View file in device app via intent from absolute file path

To make this easy, I use a Kotlin extension for strings. Call this on your absolute path string to get the Intent.

```
import android.app.Activity
import android.content.Context
import android.content.Intent
import android.support.v4.app.ShareCompat
import android.support.v4.content.FileProvider
import android.webkit.MimeTypeMap
import com.curiosityio.androidboilerplate.extensions.getFileExtensionWithoutDot
import java.io.File

fun String.getShareIntentForFile(context: Activity): Intent {
    val mimeTypeMap = MimeTypeMap.getSingleton()
    val mimeType = mimeTypeMap.getMimeTypeFromExtension(this.getFileExtensionWithoutDot())

    val nameOfApp = context.packageName
    val uri = FileProvider.getUriForFile(context, "$nameOfApp.fileprovider", File(this))

    return ShareCompat.IntentBuilder.from(context)
            .setStream(uri) // uri from FileProvider
            .setType(mimeType)
            .intent
            .setAction(Intent.ACTION_VIEW) //Change if needed
            .setDataAndType(uri, mimeType)
            .addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION)
}
```

# Associate your app with a domain name to enforce intents to launch your app

When an app attempts to view a URL via an Intent, you may receive a dialog from the OS asking you what app to launch that URL Intent with. Browsers, email apps, social media apps can all open URLs so you are probably going to see this dialog. 

But what about URLs that belong to your app? You probably want your app to open and skip the dialog. 

Here is how you associate your URL with your app:

* Wherever you are hosting your website from, you need to add a directory to the root called `.well-known`. 

*Note: If you are using a website builder like Wix or Squarespace then you may not be able to create directories and upload files to the web server. If this is the case, then I suggest that you create a subdomain (like: `app.example.com`) that you designate as the URL path for all of the URLs you use associated with your app and a subdomain that you are able to host the files yourself on a web server.*

* Inside of this `.well-known` directory, you will create a file `assetlinks.json` and paste the following inside of it:

```
[{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "com.example.yourapp",
    "sha256_cert_fingerprints":
    ["AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA",
    "BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB"]
  }
},
{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "com.example.yourapp.debug",
    "sha256_cert_fingerprints":
    ["AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA"]
  }
},
{
  "relation": ["delegate_permission/common.handle_all_urls"],
  "target": {
    "namespace": "android_app",
    "package_name": "com.example.yourapp.beta",
    "sha256_cert_fingerprints":
    ["AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA:AA",
    "BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB:BB"]
  }
}]
```

Above, it is assumed that you have 3 separate apps. This could be 3 separate apps for 1 of your apps (debug, beta, and production version of the same app) or if you have multiple apps you would simply add or remove entries above for each of them. 

For each entry, you need to set the `package_name` and the `sha256_cert_fingerprints` for your app. The fingerprints can be obtained with the command: `keytool -list -v -keystore my-release-key.keystore`

* If you want to use Docker to host this very simple website for yourself, here is a setup to do that:

```
version: '2'
services:
  app-site:
    image: "nginx:1-alpine"
    restart: always
    container_name: app-site
    expose:
      - "80"
    volumes:
      - ./app-site:/usr/share/nginx/html:ro
    environment:
      - NODE_ENV=production
      - VIRTUAL_HOST=app.example.com
      - LETSENCRYPT_HOST=app.example.com
      - LETSENCRYPT_EMAIL=youremail@example.com
      - VIRTUAL_PORT=80

networks:
  default:
    external:
      name: nginx-proxy-network
```

The above is assuming you have an nginx-proxy image like the following also deployed that will be your proxy and your SSL provider:

```
version: '2'
services:
  nginx-proxy:
    image: jwilder/nginx-proxy:alpine
    container_name: nginx-proxy
    restart: always
    labels:
      - "com.github.jrcs.letsencrypt_nginx_proxy_companion.nginx_proxy"
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
      - /usr/share/nginx/html
      - /etc/docker/nginx-proxy/ssl:/etc/nginx/certs:ro
      - /home/core/nginx/vhost.d:/etc/nginx/vhost.d
  lets-encrypt-nginx-proxy-companion:
    image: jrcs/letsencrypt-nginx-proxy-companion
    container_name: lets-encrypt-nginx-proxy-companion
    restart: always
    volumes_from:
      - nginx-proxy
    volumes:
      - /etc/docker/nginx-proxy/ssl:/etc/nginx/certs:rw
      - /var/run/docker.sock:/var/run/docker.sock:ro

networks:
  default:
    external:
      name: nginx-proxy-network
```

* Lastly, in your app's manifest, add the following changes to your intent to launch your app:

```
    <intent-filter android:autoVerify="true">
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="https" android:host="app.example.com" />
    </intent-filter>

```

Above, we are adding the `android:autoVerify="true"` statement as well as `<data android:scheme="https" android:host="app.example.com" />` which you need to edit to match the URL of your app where the `.well-known` directory is hosted. 

* Now, to test you did all of this correctly. [Check out the official docs and scroll to the testing section](https://developer.android.com/training/app-links/verify-site-associations)

For more details, check out the [official docs](https://developer.android.com/training/app-links/verify-site-associations). 