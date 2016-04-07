---
name: Android menus
---

# Create menu for toolbar

* Create menu in `res/menu/`
```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <item android:id="@+id/account"
        android:icon="@drawable/account_icon"
        android:title="@string/account"
        app:showAsAction="ifRoom"/>

    <item android:id="@+id/settings"
        android:title="@string/settings"
        app:showAsAction="never"/>        
</menu>
```
*Note:* We are using the `app:` namespace for the showAsAction attribute as we are using AppCompat. If you are not, replace that part with `android:`.

* In your activity, set the menu to your toolbar:
```
@Override
public boolean onCreateOptionsMenu(Menu menu) {
    getMenuInflater().inflate(R.menu.main_menu, menu);

    return true;
}

@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.account:
            goToAccountScreen();
            return true;
        case R.id.settings:
            goToSettingsScreen();
            return true;
        default:
            return super.onOptionsItemSelected(item);
    }
}
```
You can also do this in the fragment if set the toolbar in the fragment:
```
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    setHasOptionsMenu(true); // <-- must set to get call to onCreateOptionsMenu.

    getActivity().setTitle(R.string.inbox);
}

@Override
public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) {
    inflater.inflate(R.menu.main_menu, menu);
}

@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case R.id.account:
            goToAccountScreen();
            return true;
        case R.id.settings:
            goToSettingsScreen();
            return true;
        default:
            // now it goes to the activity where it has a chance to respond to menu options. 
            return super.onOptionsItemSelected(item);
    }
}
```

# Change menu dynamically

If you have the need to dynamically change the menu in a toolbar:

After the system calls `onCreateOptionsMenu()`, it retains an instance of the Menu you populate and will not call onCreateOptionsMenu() again unless the menu is invalidated for some reason. However, you should use onCreateOptionsMenu() only to create the initial menu state and not to make changes during the activity lifecycle.

If you want to modify the options menu based on events that occur during the activity lifecycle, you can do so in the onPrepareOptionsMenu() method. This method passes you the Menu object as it currently exists so you can modify it, such as add, remove, or disable items. (Fragments also provide an onPrepareOptionsMenu() callback.)

On Android 2.3.x and lower, the system calls onPrepareOptionsMenu() each time the user opens the options menu (presses the Menu button).

On Android 3.0 and higher, the options menu is considered to always be open when menu items are presented in the app bar. When an event occurs and you want to perform a menu update, you must call invalidateOptionsMenu() to request that the system call onPrepareOptionsMenu().
