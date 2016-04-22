---
name: Animations
---

# Shared element activity transitions

*Note: This feature is only available on Lollipop and above with no support library support. So we will enable it only for v21 and above.*

1. Enable this feature in your theme. You must do it in your v21/styles.xml file.
```
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        ...
        <!-- enable android shared element activity transition animations -->
        <item name="android:windowContentTransitions">true</item>
        ...
    </style>

</resources>
```

2. In your layout files for *both* activities, add a transitionName attribute to your shared element views.

Your source activity: `activity_source.xml` for example:
```
<com.pkmmte.view.CircularImageView
    android:id="@+id/profile_imageview"
    android:layout_width="131dp"
    android:layout_height="131dp"
    android:transitionName="@string/user_profile_pic_transition_name"
    .../>
```

Your destination activity: `activity_destination.xml` for example:
```
<com.pkmmte.view.CircularImageView
    android:id="@+id/user_profile_imageview"
    android:layout_width="57dp"
    android:layout_height="57dp"    
    android:transitionName="@string/user_profile_pic_transition_name"
    .../>
```

As you can see, the IDs for each view is different but that doesn't matter.

3. Start your destination activity from your source activity. I created this protected function in my BaseFragment and BaseActivity to do this easily.
```
protected void startActivityWithTransition(Intent intent, View transitionView, int transitionName) {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
        ActivityOptionsCompat options = ActivityOptionsCompat.makeSceneTransitionAnimation(this, transitionView, getString(transitionName)); // change 'this' to 'getActivity()' if using a fragment.
        startActivity(intent, options.toBundle());
    } else {
        startActivity(intent);
    }
}
```

Now call this function:
```
startActivityWithTransition(new Intent(this, DestinationActivity.class), sourceImageView, getString(R.string.user_profile_pic_transition_name));
```

*Advanced: If you want to do a transition with more then one view.*
I use this protected function in my BaseFragment and BaseActivity:
```
protected void startActivityWithTransitions(Intent intent, Pair<View, String>... transitions) {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
        ActivityOptionsCompat options = ActivityOptionsCompat.makeSceneTransitionAnimation(this, transitions);
        startActivity(intent, options.toBundle());
    } else {
        startActivity(intent);
    }
}
```

Call is via:
```
startActivityWithTransitions(new Intent(this, DestinationActivity.class), Pair.create(sourceImageView, getString(R.string.user_profile_pic_transition_name)), Pair.create(sourceTextView, getString(R.string.user_profile_name)));
```

4. Replace all of your `finish()` calls in the destination activity with: `supportFinishAfterTransition()`. This will reverse the animation back.

That is it. You can setup custom animations and do this between fragments as well. [This is a good guide quickly going over them](https://guides.codepath.com/android/Shared-Element-Activity-Transition)
