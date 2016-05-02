---
name: Toolbar
---

# Up button

1. Set your toolbar as the actionbar.
```
setSupportActionBar(mToolbar);
```

2. Tell your actionbar to make the home button the up button:
```
getSupportActionBar().setDisplayHomeAsUpEnabled(true);
```

3. Create listener for when press:
```
@Override
public boolean onOptionsItemSelected(MenuItem item) {
    switch (item.getItemId()) {
        case android.R.id.home:
            finish();
            return true;
        default:
            return super.onOptionsItemSelected(item);
    }
}
```

# Transparent toolbar

```
<android.support.v7.widget.Toolbar
    ...
    android:background="@color/transparent_toolbar"/>
```
Then create the color resource:
```
<color name="toolbar_transparent">#00FFFFFF</color>
```

# Style toolbar home/up and menu button color

* In your `res/values/styles.xml` file, create the following style:
```
<style name="ToolbarStyle" parent="@style/ThemeOverlay.AppCompat.ActionBar">
    <item name="colorControlNormal">@android:color/white</item>
</style>
```
Above will set the up button and menu button 3 dots to white color.

* Set this style with `app:theme=` like below:
```
<android.support.v7.widget.Toolbar xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_height="wrap_content"
    android:layout_width="match_parent"
    android:minHeight="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    app:theme="@style/ToolbarStyle" />
```
