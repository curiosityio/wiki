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
            return super.onOptionsItemSelected(item);
    }
}
```
