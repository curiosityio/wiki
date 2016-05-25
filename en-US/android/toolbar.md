---
name: Toolbar
---

# Add toolbar to layout

```
<android.support.v7.widget.Toolbar
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:background="?attr/colorPrimary"
    android:title="@string/title_here"/>
```

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

# Add views to Toolbar

The Toolbar is a ViewGroup. That means you can add views to it.

Lets add a TextView to a Toolbar that is our own custom title.

```
<android.support.v7.widget.Toolbar
    android:id="@+id/toolbar"
    android:layout_width="match_parent"
    android:layout_height="?attr/actionBarSize"
    android:background="?attr/colorPrimary">

    <TextView
        android:id="@+id/toolbar_title_textview"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:textColor="@android:color/white"
        android:textSize="20sp"
        android:ellipsize="none"
        android:maxLines="1"
        android:gravity="center_vertical"/>
</android.support.v7.widget.Toolbar>
```

Then in our Activity/Fragment that contains this Toolbar, you can add click listeners to the TextView, anything that you can do with a TextView.

Even though we have added a view to the Toolbar, the Toolbar still acts like a normal Toolbar with menus, home button, etc. 
