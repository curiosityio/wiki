---
name: Scheduling
---

If you want to schedule a task on Android, you found the right place.

There are many use cases for scheduling. Here are some examples:

* "Run this task the very next time that a user is on WiFi."
* "Run this task every 10 minutes even after a reboot."

You can add many options to a job to specify exactly when to run a task.

To schedule a job, Android has the `JobScheduler` API. However, JobScheduler is available only APIs 21+. This is why we are going to talk about how to use [GCM Network Manager](https://developers.google.com/cloud-messaging/network-manager). GCM Network Manager uses JobScheduler on devices running 21+ but works on APIs below as well.

# Schedule a task on GCM Network Manager.

* Install GCM core to your project via gradle.

```
compile "com.google.android.gms:play-services-gcm:9.8.0"
```

* Create a subclass of `GcmTaskService`:

```
import com.google.android.gms.gcm.GcmNetworkManager
import com.google.android.gms.gcm.GcmTaskService
import com.google.android.gms.gcm.TaskParams

open class TaskService(): GcmTaskService() {

    override fun onRunTask(p0: TaskParams?): Int {
        // run your task here. It will run in a background thread created for you by GcmTaskService.
        // do API calls. talk to your database. do whatever you need here that does not require UI.

        return GcmNetworkManager.RESULT_SUCCESS
    }

}
```

* Add entry in your manifest file:

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.levibostian.foo">

    <uses-permission android:name="com.google.android.gms.permission.BIND_NETWORK_TASK_SERVICE"/> <!-- add for GMS -->
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/> <!-- add only if you need task to be persisted -->

    <application
        android:theme="@style/AppTheme">
        <activity android:name=".activity.LaunchActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <!-- add the below entry for registering your GCM service -->
        <service
            android:name=".receiver.TaskService"
            android:exported="true"
            android:permission="com.google.android.gms.permission.BIND_NETWORK_TASK_SERVICE">
                <intent-filter>
                    <action android:name="com.google.android.gms.gcm.ACTION_TASK_READY" />
                </intent-filter>
        </service>
    </application>
</manifest>
```

* In your Activity, start your task:

```
public class MainActivity extends BaseActivity {

    private GcmNetworkManager mGcmNetworkManager;
    private static final String TASK_SERVICE_TAG = "tasksServiceTag.mainActivity";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mGcmNetworkManager = GcmNetworkManager.getInstance(this);
        startRecurringTask();

        ...
    }    

    @Override
    protected void onDestroy() {
        super.onDestroy();

        stopRecurringTask();
    }

    private void stopRecurringTask() {
        mGcmNetworkManager.cancelTask(TASK_SERVICE_TAG, TaskService.class);
    }

    private void startRecurringTask() {
        PeriodicTask task = new PeriodicTask.Builder()
                .setService(PendingApiTasksService.class) // name of the service we created above.
                .setTag(TASK_SERVICE_TAG) // identify the service.
                .setRequiredNetwork(Task.NETWORK_STATE_CONNECTED) // run when wifi or network connection connected.
                .setPeriod(60L) // 60 seconds
                .build();

        mGcmNetworkManager.schedule(task);
    }

}
```

Really, that is it. Check out the docs on [PeriodicTask Builder](https://developers.google.com/android/reference/com/google/android/gms/gcm/PeriodicTask.Builder) to find out what else you can use to schedule your task.
