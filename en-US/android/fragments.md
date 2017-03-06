---
name: Fragments
---

# Animate transitions

Here is an example for how to do a fragment manager transaction with one fragment fading out while the new fragment fades in.

1. Create the animation to fade out:
```
<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:interpolator/accelerate_quad"
    android:valueFrom="1"
    android:valueTo="0"
    android:propertyName="alpha"
    android:duration="@android:integer/config_shortAnimTime"/>
```

2. Create the animation to fade in:
```
<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:interpolator/accelerate_quad"
    android:valueFrom="0"
    android:valueTo="1"
    android:propertyName="alpha"
    android:duration="@android:integer/config_shortAnimTime"/>
```

3. Add the transition to the fragment manager transaction:
```
private void swapFragmentContainer(Fragment fragmentToSwapWith) {
    getFragmentManager().beginTransaction()
                        .setCustomAnimations(R.animator.fade_in_animation, R.animator.fade_out_animation)
                        .replace(R.id.fragment_container, fragmentToSwapWith)
                        .commit();
    }
```

# Show dialog fragments

There is an issue with displaying dialog fragments using the `show()` function. It gives you an error if you try to show a dialog fragment in onactivityresult or one like it. Error something to do with onSaveInstanceState.

Either way, here is how to get around it:

```
mDialogFragment = new DialogFragment();
mDialogFragment.show(getChildFragmentManager(), mDialogFragment.getTag());
```

# Dismissing dialog fragments

Just like above with showing, we must allow state loss when dismissing.

```
mDialogFragment = new DialogFragment();
// show dialog fragment
mDialogFragment.dismissAllowingStateLoss();
```

# Fragments sitting over another fragment

When you .add() a fragment to a fragment manager, you may run into a couple of issues.

## Background fragment is visible. Fragment on top is transparent.

Solution: For the fragment sitting on top of the background fragment, set it's background color so it is not invisible anymore.

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="20dp"
    android:clickable="true"     
    android:background="@android:color/white">  <--------------------------

    ...

</RelativeLayout>
```

## Clicking on 'dead' areas of the front facing fragment can click elements of the fragment behind it.

Solution: For the fragment sitting on top of the background fragment, set it to be clickable.

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:padding="20dp"
    android:clickable="true"     <--------------------------
    android:background="@android:color/white">

    ...

</RelativeLayout>
```
