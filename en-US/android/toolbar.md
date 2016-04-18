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

# Style tabbar

If you want to change the underline color, text color, underline height, of tabs for example:

* In your `res/values/styles.xml` file, create the following style:
```
<style name="AppTabLayout" parent="Widget.Design.TabLayout">
    <item name="tabMaxWidth">@dimen/tab_max_width</item>
    <item name="tabIndicatorColor">?attr/colorAccent</item>
    <item name="tabIndicatorHeight">4dp</item>
    <item name="tabPaddingStart">6dp</item>
    <item name="tabPaddingEnd">6dp</item>
    <item name="tabBackground">?attr/selectableItemBackground</item>
    <item name="tabTextAppearance">@style/AppTabTextAppearance</item>
    <item name="tabSelectedTextColor">@color/range</item>
</style>
```
I usually delete a lot of these attributes and only keep the indicator color one.

* Set this style with `style=` like below in your tablayout:
```
<android.support.design.widget.TabLayout
    android:id="@+id/tablayout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    style="@style/AppTabLayout"/>
```
