---
name: SMS
---

# Reading incoming SMS messages

*Note: If your application is just reading SMS, this will work. If you want to send them, read [this blog post](https://android-developers.googleblog.com/2013/10/getting-your-sms-apps-ready-for-kitkat.html) from Android team on how to do so as times have changed.*

* Add permission to manifest

```
<uses-permission android:name="android.permission.RECEIVE_SMS" />
```

* Create receiver for listening to SMS:
*Note: I cannot guarantee this works for all versions of Android. I have not tested it on newer Android versions yet. I know there are new APIs for doing this. Below is somewhat unofficial even though this is what all Android devs did before official APIs came out later.*

```
import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.support.v4.app.NotificationCompat.getExtras
import android.os.Bundle
import android.provider.Telephony
import android.telephony.SmsMessage
import java.util.*

class ReceiveSMSBroadcastReceiver : BroadcastReceiver() {

    override fun onReceive(p0: Context?, intent: Intent?) {
        if (Telephony.Sms.Intents.SMS_RECEIVED_ACTION == intent!!.action) {
            val bundle = intent.extras
            if (bundle != null) {
                try {
                    val pdus = bundle.get("pdus") as Array<Any>
                    val messages: ArrayList<SmsMessage> = ArrayList()

                    pdus.forEach { pdusObject ->
                        val latestMessage = SmsMessage.createFromPdu(pdusObject as ByteArray?)

                        messages.add(latestMessage)
                        val messageFrom = latestMessage.originatingAddress
                        val messageBody = latestMessage.messageBody

                        // we now has the body of the text message and who it was from.
                    }
                } catch (e: Exception) {
                    e.printStackTrace()
                }
            }
        }
    }

}
```

* Setup receiver in manifest:

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.levibostian.foo">

    <uses-permission android:name="android.permission.RECEIVE_SMS" />

    <application
        android:name="com.levibostian.foo.MainApplication"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme">
        <activity android:name="com.levibostian.foo.activity.MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <receiver android:name=".receiver.ReceiveSMSBroadcastReceiver">
            <intent-filter android:priority="1000">
                <action android:name="android.provider.Telephony.SMS_RECEIVED" />
            </intent-filter>
        </receiver>        
    </application>

</manifest>
```

* If running Nougat and above, make sure to ask user for permission to `RECEIVE_SMS` in app!
