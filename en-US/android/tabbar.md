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

# Implement tab bar

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

*Note: FragmentPagerAdapter is good for a small number of finite fragments. If you have lots or an "infinite" number, use FragmentStatePagerAdapter instead.*

* [Get the instance of the currently shown fragment from a FragmentStatePagerAdapter](http://stackoverflow.com/a/8886019/1486374)
* [Get the instance of the currently shown fragment from a FragmentPagerAdapter](http://stackoverflow.com/a/7393477/1486374)
