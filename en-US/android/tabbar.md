---
name: Tab bar
---

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

# Implement tab bar with fragments and viewpager.

* Create layout file

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.design.widget.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:theme="@style/AppTheme">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            app:layout_scrollFlags="scroll|enterAlways"
            android:title="@string/applications"
            app:theme="@style/ToolbarStyle"/>

        <android.support.design.widget.TabLayout
            android:id="@+id/tablayout"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            style="@style/AppTabLayout"/>

    </android.support.design.widget.AppBarLayout>

    <android.support.v4.view.ViewPager
        android:id="@+id/viewpager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

</android.support.design.widget.CoordinatorLayout>
```

styles. values:

```
<style name="ToolbarStyle" parent="@style/ThemeOverlay.AppCompat.ActionBar">
    <item name="colorControlNormal">@android:color/white</item>
</style>

<style name="AppTabLayout" parent="Widget.Design.TabLayout">
    <item name="tabIndicatorColor">@android:color/white</item>
    <item name="tabBackground">?attr/selectableItemBackground</item>
    <item name="tabSelectedTextColor">@android:color/white</item>
    <item name="colorControlNormal">@android:color/white</item>
</style>
```

values-v21:

```
<style name="ToolbarStyle" parent="@style/ThemeOverlay.AppCompat.ActionBar">
    <item name="colorControlNormal">@android:color/white</item>
</style>

<style name="AppTabLayout" parent="Widget.Design.TabLayout">
    <item name="tabIndicatorColor">@android:color/white</item>
    <item name="tabBackground">?attr/selectableItemBackground</item>
    <item name="tabSelectedTextColor">@android:color/white</item>
    <item name="colorControlNormal">@android:color/white</item>
</style>
```

* Setup in Activity or Fragment:

```
package your.package.name.here;

import android.content.SharedPreferences;
import android.os.Bundle;
import android.support.annotation.Nullable;
import android.support.design.widget.Snackbar;
import android.support.design.widget.TabLayout;
import android.support.v4.view.ViewPager;
import android.support.v7.app.AppCompatActivity;
import android.support.v7.widget.Toolbar;
import android.view.LayoutInflater;
import android.view.Menu;
import android.view.MenuInflater;
import android.view.MenuItem;
import android.view.View;
import android.view.ViewGroup;
import io.realm.Realm;
import org.greenrobot.eventbus.EventBus;

import javax.inject.Inject;

public class EmailListFragment extends BaseFragment {

    private static final int INBOX_PAGER_INDEX = 0;
    private static final int ARCHIVE_PAGER_INDEX = 1;

    private InboxListFragment mInboxListFragment;
    private ArchiveListFragment mArchiveListFragment;

    private ViewPager mViewPager;
    private Toolbar mToolbar;
    private TabLayout mTabLayout;

    private FragmentViewPagerAdapter mViewPagerAdapter;

    public static final EmailListFragment newInstance() {
        return new EmailListFragment();
    }

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_application_list, container, false);

        mViewPager = (ViewPager) view.findViewById(R.id.viewpager);
        mToolbar = (Toolbar) view.findViewById(R.id.toolbar);
        mTabLayout = (TabLayout) view.findViewById(R.id.tablayout);

        setupView();

        return view;
    }

    private void setupView() {
        ((AppCompatActivity) getActivity()).setSupportActionBar(mToolbar);

        setupViewPager();

        mTabLayout.setupWithViewPager(mViewPager);

        mInboxListFragment = (InboxListFragment) mViewPagerAdapter.getItem(INBOX_PAGER_INDEX);
        mArchiveListFragment = (ArchiveListFragment) mViewPagerAdapter.getItem(ARCHIVE_PAGER_INDEX);
    }

    private void setupViewPager() {
        mViewPagerAdapter = new FragmentViewPagerAdapter(getActivity().getFragmentManager());
        mViewPagerAdapter.addFragment(getString(R.string.inbox), InboxListFragment.newInstance());
        mViewPagerAdapter.addFragment(getString(R.string.archive), ArchiveListFragment.newInstance(this));

        mViewPager.setAdapter(mViewPagerAdapter);
    }

    @Override
    public void goToInbox() {
        mViewPager.setCurrentItem(INBOX_PAGER_INDEX);
    }

}
```

Here is the `FragmentViewPagerAdapter`

```
package your.package.name.here;

import android.app.Fragment;
import android.app.FragmentManager;
import android.support.v13.app.FragmentPagerAdapter;

import java.util.ArrayList;

public class FragmentViewPagerAdapter extends FragmentPagerAdapter {

    private ArrayList<FragmentItem> mFragmentItems = new ArrayList<>();

    public FragmentViewPagerAdapter(FragmentManager fragmentManager) {
        super(fragmentManager);
    }

    public void addFragment(String title, Fragment fragment) {
        mFragmentItems.add(new FragmentItem(title, fragment));
    }

    @Override
    public Fragment getItem(int position) {
        return mFragmentItems.get(position).fragment;
    }

    @Override
    public int getCount() {
        return mFragmentItems.size();
    }

    @Override
    public CharSequence getPageTitle(int position) {
        return mFragmentItems.get(position).title;
    }

    public class FragmentItem {

        private String title;
        private Fragment fragment;

        public FragmentItem(String title, Fragment fragment) {
            this.title = title;
            this.fragment = fragment;
        }

    }

}
```

Because the FragmentPagerAdapter classes load more then 1 fragment at a time, you may need to know if a fragment is the one currently visible to the user (for setting the title for example).

Add this to your fragments that get instantiated in the pageradapter:

```
@Override
public void setUserVisibleHint(boolean isVisibleToUser) {
    super.setUserVisibleHint(isVisibleToUser);

    if (isVisibleToUser) {
        getActivity().setTitle("foo");
    }
}
```

*Note: setUserVisibleHint() may be called before onCreate or may be called after onCreate. Therefore, it is best to do the following:*

```
@Override
public void setUserVisibleHint(boolean isVisibleToUser) {
    super.setUserVisibleHint(isVisibleToUser);

    mFragmentVisibleToUser = isVisibleToUser;

    if (getActivity != null && mFragmentVisibleToUser) {
        getActivity().setTitle("foo");
    }
}

@Override
public void onResume() {
    super.onResume();

    if (mFragmentVisibleToUser) {
        getActivity().setTitle("foo");
    }
}
```

*Note: FragmentPagerAdapter is good for a small number of finite fragments. If you have lots or an "infinite" number, use FragmentStatePagerAdapter instead.*

* [Get the instance of the currently shown fragment from a FragmentStatePagerAdapter](http://stackoverflow.com/a/8886019/1486374)
* [Get the instance of the currently shown fragment from a FragmentPagerAdapter](http://stackoverflow.com/a/7393477/1486374)

## IllegalStateException: The application's PagerAdapter changed the adapter's contents without calling PagerAdapter#notifyDataSetChanged! Expected adapter item count: 10, found: 0

[Original help provided here](http://stackoverflow.com/a/24248410/1486374)

I received this error in an app of mine when starting up the app. The reason of the problem is the Pager can be refreshed during some measurement and in this case will refresh count of your adapter. If this count will not equals with saved count you will see the error.

```
public abstract class ViewPagerCursorAdapter extends PagerAdapter {

    private int mCount;

    public ViewPagerCursorAdapter(Context ctx, Cursor cursor, int resource) {
        super();
        ...
        mCount = cursor.getCount();
        ...
    }

    @Override
    public int getCount() {
        return mCount;
    }

    public Cursor swapCursor(Cursor newCursor) { // <---- if you ever decide to edit the mCount, doesnt matter where or when, call notifyDataSetChanged().
        ...
        mCount = newCursor.getCount();
        notifyDataSetChanged();
    }
}
```

My issue was that the getCount() method returned a dynamic number depending on the user's state. I instead set the count in the constructor and if it ever changes with state, I need to call notifyDataSetChanged().

# Implement tabbar without viewpager.

* Add tabbarlayout to your layout.

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:custom="http://schemas.android.com/apk/res-auto"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                xmlns:app="http://schemas.android.com/apk/res-auto"
                xmlns:tools="http://schemas.android.com/tools">

    <android.support.design.widget.TabLayout
        android:id="@+id/foo_tablayout"
        style="@style/AppTabLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="@android:color/white"
        app:tabGravity="fill"
        app:tabMode="fixed" />

</RelativeLayout>
```

* Add tabs and tab listener to activity/fragment

```
override fun onCreateView(inflater: LayoutInflater?, container: ViewGroup?, savedInstanceState: Bundle?): View? {
    val view: View = inflater!!.inflate(R.layout.fragment_foo_layout, container, false)

    val tabbar = view.foo_tablayout as TabLayout
    tabbar.addTab(tabbar.newTab().setText(R.string.first_tab_text))
    tabbar.addTab(tabbar.newTab().setText(R.string.second_tab_text))

    tabbar.addOnTabSelectedListener(object : TabLayout.OnTabSelectedListener {
        override fun onTabReselected(tab: TabLayout.Tab?) {
        }
        override fun onTabUnselected(tab: TabLayout.Tab?) {
        }
        override fun onTabSelected(tab: TabLayout.Tab?) {
            when (tab?.position) {
                0 -> showFirstTabScreen()
                1 -> showSecondTabScreen()
            }
        }
    })    

    return view
}
```

# Change look of TabBar

You can have a full width equal size tabs, left/right/center small tabs, doesn't matter.

```
<FrameLayout
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@color/same_background_as_tablayout_below">

    <android.support.design.widget.TabLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="left"
        app:tabMaxWidth="120dp"/>
</FrameLayout>
```

This above makes small tabs, left align the bar and make it "appear" full screen with that framelayout behind it.

```
<android.support.design.widget.TabLayout
    android:id="@+id/foo_tablayout"
    style="@style/AppTabLayout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@android:color/white"
    app:tabGravity="fill"
    app:tabMode="fixed" />
```

This makes a full width bar where each tab gets equal with.

Check out [TabBarLayout](https://developer.android.com/reference/android/support/design/widget/TabLayout.html) docs for the XML options that you have for the bar.

# Change color of text and icons in tab when they are selected in tab bar.

* For text you have in tabs, this is very easy. Android takes care of this for you.

```
<android.support.design.widget.TabLayout
    android:id="@+id/foo_tablayout"
    style="@style/AppTabLayout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="@android:color/white"
    app:tabSelectedTextColor="@color/selected_tab_color"
    app:tabTextColor="@color/unselected_tab_color"
    app:tabIndicatorColor="@color/underline_color_selected_tab"/>
```

Using XML attribute `app:tabSelectedTextColor` you can change the color of the selected state.
`app:tabTextColor` is the color of the default state.
`app:tabIndicatorColor` is the color of the underline below the selected tab.

* For icons, it takes a little more work.

Create a new drawable XML resource file in `res/main/drawable/`.

Then, inside of that new XML drawable file specify the drawable file to use for the selected and unselected state:

```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/icon_for_selected_state" android:state_selected="true" />
    <item android:drawable="@drawable/icon_for_unselected_state" android:state_selected="false" />
</selector>
```

Then, when creating this new tab to your tabbar layout, use the drawable XML resource file you created above instead of your drawable icon.

```
mTabBarLayout.addTab(newTab().setIcon(R.drawable.edit_process_text_components_text_type_bullet))
```
