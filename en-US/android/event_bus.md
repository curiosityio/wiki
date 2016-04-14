---
name: Eventbus
---

# Install into app

* Add to build.gradle:
```
compile 'org.greenrobot:eventbus:3.0.0'
```
[Check to see if this is the most up-to-date version](https://github.com/greenrobot/EventBus/releases) of the library.

* Create an event class. This will be when the app settings are modified.
```
public class SettingsModifiedEvent {
}
```
You can add add data to this class if you want:
```
public class SettingsModifiedEvent {

    public String message;

    public SettingsModifiedEvent(String message) {
        this.message = message;
    }
}
```

* Objects that need to listen for events, have them register to the event bus:

Fragments/activities:
```
@Override
public void onResume() {
    super.onResume();

    EventBus.getDefault.register(this);
}
...
@Override
public void onPause() {
    EventBus.getDefault.unregister(this);

    super.onPause();
}
```

Custom views:
```
@Override
protected void onAttachedToWindow() {
    super.onAttachedToWindow();

    EventBus.getDefault().register(this);
}

@Override
protected void onDetachedFromWindow() {
    EventBus.getDefault().unregister(this);

    super.onDetachedFromWindow();
}
```

* Objects subscribe to events that they care about:
```
@Subscribe
public void onEvent(SettingsModifiedEvent event) {
    // Do something
}
```
**Advanced:** You can [specify what thread the subscribe thread is executed on](http://greenrobot.org/eventbus/documentation/delivery-threads-threadmode/). 

* One last thing. The EventBus has to post a new event when the event happens. So in our example, when the settings are actually modified in a PreferenceFragment:
```
@Override
public void onSharedPreferenceChanged(SharedPreferences sharedPreferences, String key) {
    EventBus.getDefault().post(new SettingsModifiedEvent());
}
```
