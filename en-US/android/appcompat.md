---
name: Android AppCompat
---

# Add shadow below toolbar/tabbar

API 21 and above have a shadow below the toolbar but for anything below it, there is no shadow. Here is how to add a shadow below the toolbar/tabbar.

* Create drawable and name is `bottom_shadow` or something:
```
<?xml version="1.0" encoding="utf-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <gradient
        android:startColor="@android:color/transparent"
        android:endColor="#33000000"
        android:angle="90">
    </gradient>
</shape>
```

* Create a view layout file called `bottom_shadow_layout` or something:
```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <View
        android:layout_width="match_parent"
        android:layout_height="8dp"
        android:background="@drawable/bottom_shadow"/>

</FrameLayout>
```

* Now add that `bottom_shadow_layout` to any layout view that you want to add it to (any view you have a toolbar or tabbar). Notice the parent RelativeLayout. That allows the RecyclerView in this example sit behind the bottom shadow to be a true shadow.
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <include layout="@layout/bottom_shadow_layout"/>

    <android.support.v7.widget.RecyclerView
        android:id="@+id/inbox_recycler_view"
        android:scrollbars="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</RelativeLayout>
```
