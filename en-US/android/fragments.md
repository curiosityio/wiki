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
