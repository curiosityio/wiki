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
        outState.putInt(BAR_BUNDLE_KEY, mBar);

        super.onSaveInstanceState(outState); // put after outstate.put_() calls to avoid illegal state exception.
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

* Parcelables

Data objects such as VO objects can be saved into bundles but must implement interface Parcelable.

There is an awesome [plugin](https://github.com/mcharmas/android-parcelable-intellij-plugin) for studio/intellij to make any VO object parcelable with a click.

This should convert regular objects into parcelable just fine.

---

#### Realm lists.

If you have a Realm Model, you can convert these to parcelable as well as they are just VO objects themselves. As long as all nested objects are also parcelable you are find.

One note with Realm however is lists. Realm requires that if you have a List<> or ArrayList<> you instead use RealmList<> (which is just a List<> that holds other realm models). To make a Realm model containing a RealmList parcelable:

```
import android.os.Parcel;
import android.os.Parcelable;
import com.fasterxml.jackson.annotation.JsonIgnoreProperties;
import io.realm.RealmList;
import io.realm.RealmObject;
import io.realm.annotations.PrimaryKey;
import io.realm.annotations.Required;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

@JsonIgnoreProperties(ignoreUnknown = true)
public class FooModel extends RealmObject implements Parcelable {

    @PrimaryKey public long id;
    public String owner;
    public Date updated_at;
    public int organization_id;
    public RealmList<TagsModel> tags;

    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel dest, int flags) {
        dest.writeLong(this.id);
        dest.writeString(this.owner);
        dest.writeLong(this.updated_at != null ? this.updated_at.getTime() : -1);
        dest.writeInt(this.organization_id);
        dest.writeList(this.tags);
    }

    public FooModel() {
    }

    protected FooModel(Parcel in) {
        this.id = in.readLong();
        this.owner = in.readString();
        long tmpUpdated_at = in.readLong();
        this.updated_at = tmpUpdated_at == -1 ? null : new Date(tmpUpdated_at);
        this.organization_id = in.readInt();
        this.tags = new RealmList<TagsModel>();
        in.readList(this.tags, TagsModel.class.getClassLoader());
    }

    public static final Creator<FooModel> CREATOR = new Creator<FooModel>() {
        @Override
        public FooModel createFromParcel(Parcel source) {
            return new FooModel(source);
        }

        @Override
        public FooModel[] newArray(int size) {
            return new FooModel[size];
        }
    };

}
```

#### Null natives.

If you are dealing with an API that returns back 'null' values for ints, bools, etc. this is how you should build your VO/Parcelable objects.

```
import android.os.Parcel;
import android.os.Parcelable;
import com.fasterxml.jackson.annotation.JsonIgnoreProperties;
import io.realm.RealmList;
import io.realm.RealmObject;
import io.realm.annotations.PrimaryKey;
import io.realm.annotations.Required;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

@JsonIgnoreProperties(ignoreUnknown = true)
public class FooModel extends RealmObject implements Parcelable {

    public int organization_id;
    public Integer possible_null_organization_id;

    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel dest, int flags) {
        dest.writeSerializable(this.possible_null_organization_id);
        dest.writeInt(this.organization_id);
    }

    public FooModel() {
    }

    protected FooModel(Parcel in) {
        this.possible_null_organization_id = (Integer) in.readSerializable();
        this.organization_id = in.readInt();
    }

    public static final Creator<FooModel> CREATOR = new Creator<FooModel>() {
        @Override
        public FooModel createFromParcel(Parcel source) {
            return new FooModel(source);
        }

        @Override
        public FooModel[] newArray(int size) {
            return new FooModel[size];
        }
    };

}
```
