---
name: Notifications
---

# Create a notification to display in notification drawer.

```
val PERMISSION_ERROR_PENDING_API_TASKS_REQUEST = 0

val notification = NotificationCompat.Builder(context)
        .setSmallIcon(R.drawable.ic_error)
        .setContentTitle(context.getString(R.string.title_here))
        .setContentText(context.getString(R.string.description_here))
        .setContentIntent(PendingIntent.getActivity(context, PERMISSION_ERROR_PENDING_API_TASKS_REQUEST, FooResolveActivity.getIntent(context), PendingIntent.FLAG_UPDATE_CURRENT)) // set the activity to launch when user presses notification.
        .setAutoCancel(true) // when user touches notification, dismiss it from drawer automatically.
        .build()
val notificationManager = context.getSystemService(NOTIFICATION_SERVICE) as NotificationManager
notificationManager.notify(PERMISSION_ERROR_PENDING_API_TASKS_REQUEST, notification) // display notification in drawer.
```

The above code will create a notification and show it to the user. You are required to set the minimum of:

* A small icon, set by setSmallIcon()
* A title, set by setContentTitle()
* Detail text, set by setContentText()

`notificationManager.notify()` takes an id at the beginning and a notification you build. If you call `notify()` with the same ID as a previous time you called `notify()` then the notification will be updated with new data. This allows you to not show multiple notifications of the same type. It prevents duplicates.

`FooResolveActivity` will be launched when the notification is touched. If the activity needs some data to react when the notification is pressed, pass it into the intent for the activity.
