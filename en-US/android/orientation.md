---
name: Orientation Android.
---

Sometimes you want to lock the orientation to landscape or portrait for an app.

You have two options:

1. Activity by activity basis in the manifest:
```
<activity android:name="MyActivity"
    android:screenOrientation="landscape"
    android:configChanges="keyboardHidden|orientation|screenSize">
...
</activity>
```
Where MyActivity is the one you want to stay in landscape.

The android:configChanges=... line prevents onResume(), onPause() from being called when the screen is rotated. Without this line, the rotation will stay as you requested but the calls will still be made.

Note: keyboardHidden and orientation are required for < Android 3.2 (API level 13), and all three options are required 3.2 or above, not just orientation.

2. Activity by activity basis in the activity code:
```
@Override
protected void onCreate(@Nullable Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_PORTRAIT);
}
```

So if you want to set it for the entire app, I use the code approach by creating a `BaseActivity` and set the orientation in the `onCreate()`. Then have all of your activities inherit the base activity.

# Saving data on orientation guidelines

Basic rules:

* Once data is sent to a fragment or activity, it is the responsibility of the receiving fragment/activity to keep it.

```
public class FooFragment extends Fragment {
    ...
    private static final String BAR_BUNDLE_KEY = "barBundleKey.fooFragment";
    ...
    private int mBar;
    ...
    public static FooFragment newInstance(int bar) {
        FooFragment fragment = new FooFragment();

        Bundle bundle = new Bundle();
        bundle.putInt(BAR_BUNDLE_KEY, bar);

        fragment.setArguments(bundle);

        return fragment;
    }

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        if (getArguments() != null) {
            mBar = getArguments().getInt(BAR_BUNDLE_KEY);
        }

        // You can also access savedInstanceState from onCreateView().
        if (savedInstanceState != null) {
            mBar = getArguments().getInt(BAR_BUNDLE_KEY);
        }
    }

    @Override
    public void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);

        outState.putInt(BAR_BUNDLE_KEY, mBar);
    }
}
```

Once data is sent to `newInstance()`, it is the responsibility of the fragment to keep that (saving in onSaveInstanceState).

* Listeners need to be set each and every time screen is rotated.

Do not set listeners in the `newInstance()` for fragments or in `getIntent()` for Activities. When screen is rotated, activities and fragments are recreated. Make sure to reset them every time.

For Fragments, this is easy. Just have a function for setting listener:

```
public class FooFragment extends Fragment {
    ...
    public interface Listener {
        void foo();
    }
    ...
    private Listener mListener;
    ...
    public void setListener(Listener listener) {
        mListener = listener;
    }
}
```

In Activities, you can set this listener after obtaining the Fragment from the Fragment Manager.

```
private static final FOO_FRAGMENT_TAG = "fooFragmentTag.activityName";
...
getFragmentManager().beginTransaction().replace(R.id.fragment_container, FooFragment.newInstance(), FOO_FRAGMENT_TAG).commit();
getFragmentManager().executePendingTransactions(); // must do this in order to find fragment by tag or it may return back null.
...
FooFragment fooFragment = (FooFragment) getFragmentManager().findFragmentByTag(FOO_FRAGMENT_TAG);
```

In fragments you may do the same as above but instead of using the `getFragmentManager()`, use `getChildFragmentManager()`.

* Maintaining fragments.

Fragment managers maintain fragments. Only add a fragment to fragment manager once per activity or fragment lifecycle.

```
private void setupViews(Bundle savedInstanceState) {
    if (savedInstanceState == null) { // if savedInstanceState is null, then it's the first time fragment has been loaded.
        addFooFragmentToContainer();
    }

    mFooFragment = (FooFragment) getChildFragmentManager().findFragmentByTag(PROCESS_STEPS_FRAGMENT_TAG);
    mFooFragment.setListAdapter(this);
}
```

* FragmentPagerAdapters.

The same rules apply as above for instantiating fragments via FragmentPagerAdapters such as (1) setting listeners via function and (2) fragments responsible for data passed to it.

```
public class FooFragmentViewPagerAdapter extends FragmentStatePagerAdapter {

    private Context mContext;
    private BarData mBarData;
    private FooFragment.Listener mListener;

    public FooFragmentViewPagerAdapter(Context context, FragmentManager fragmentManager, BarData bar, FooFragment.Listener listener) {
        super(fragmentManager);

        mContext = context;
        mBarData = bar;
        mListener = listener;
    }

    // here we set data to fragments.
    @Override
    public Fragment getItem(int position) {
        return FooFragment.newInstance(mBarData);
    }

    @Override
    public int getCount() {
        return 5;
    }

    // here we are able to set listeners for fragments.
    @Override
    public Object instantiateItem(ViewGroup container, int position) {
        FooFragment fragment = (FooFragment) super.instantiateItem(container, position);

        fragment.setListener(mListener);

        return fragment;
    }

}
```
