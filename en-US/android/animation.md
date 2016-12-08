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

# Rotation animation

* Create file in `res/anim` for the animation:

```
<?xml version="1.0" encoding="utf-8"?>
<rotate
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:fromDegrees="-90" <---- negative means go counter clockwise.
    android:toDegrees="0"
    android:pivotX="50%" <------- half of view width is pivot point of the animation
    android:pivotY="50%" <------- half of view height is pivot point of the animation
    android:fillAfter="true" <------ after animation complete, keep the view where the animation ended.
    android:fillEnabled="true" <------ after animation complete, keep the view where the animation ended.
    android:interpolator="@android:anim/linear_interpolator" <---- interpolator to smoothly transition
    android:duration="200"/>
```

* Apply animation to view:

```
view.startAnimation(AnimationUtils.loadAnimation(mContext, R.anim.name_of_file_created_above));
```

# Fragment transaction animations

* Create the animation files for the fragment entering and the fragment exiting:

The slide in and slide out effect is pretty popular so I will go over that one.

Here is `enter_from_right` in `/res/anim` directory.

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="false">
    <translate
        android:fromXDelta="100%" android:toXDelta="0%"
        android:fromYDelta="0%" android:toYDelta="0%"
        android:duration="200" />
</set>
```

Here is `exit_to_left`.

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="false">
    <translate
        android:fromXDelta="0%" android:toXDelta="-100%"
        android:fromYDelta="0%" android:toYDelta="0%"
        android:duration="200"/>
</set>
```

* Add the animations to the fragment transaction:

```
getFragmentManager()
        .beginTransaction()
        .setCustomAnimations(R.anim.enter_from_right, R.anim.exit_to_left)
        .replace(R.id.fragment_container, FooFragment.newInstance())
        .commit();
```

*Note: You must set `.setCustomAnimations()` before the .add() or .replace() call or the animation will not happen.*

# Play multiple animations at once

ObjectAnimators are handy ways to animate properties of objects. When you want to run multiple animations on one object, you create an AnimatorSet:

```
AnimatorSet set = new AnimatorSet();
set.playTogether(ObjectAnimator.ofFloat(viewToAnimate, "alpha", 0, 1),
        ObjectAnimator.ofFloat(viewToAnimate, "translationX", distance, 0));
set.setDuration(100);

return set;
```
