---
name: Navigation Drawer
---

# Implementing navigation drawer

* Add DrawerLayout to layout file:

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.widget.DrawerLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/nav_drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <!-- Here is your main content -->
    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.v7.widget.Toolbar
            android:id="@+id/toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"/>

        <View
            android:layout_width="match_parent"
            android:layout_height="8dp"
            android:background="@drawable/bottom_drop_shadow"
            android:layout_below="@id/toolbar"/>

        <FrameLayout
            android:id="@+id/fragment_container"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_below="@id/toolbar"/>
    </RelativeLayout>

    <!-- here is your navigation drawer view (a listview here) -->
    <ListView
        android:id="@+id/nav_drawer"
        android:layout_width="240dp"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        android:choiceMode="singleChoice"
        android:divider="@android:color/transparent"
        android:dividerHeight="0dp"
        android:background="@color/navigatin_drawer_color"/>

</android.support.v4.widget.DrawerLayout>
```

* Configure navigation drawer in activity:

```
import android.content.res.Configuration;
import android.os.Bundle;
import android.support.v4.widget.DrawerLayout;
import android.support.v7.app.ActionBarDrawerToggle;
import android.support.v7.widget.Toolbar;
import android.view.MenuItem;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ListView;

import java.util.ArrayList;

public class MainActivity extends BaseActivity {

    private static final int FRAGMENT_CONTAINER_ID = R.id.fragment_container;

    private DrawerLayout mDrawerLayout;
    private ListView mNavDrawerListView;
    private NavDrawerListViewAdapter mNavDrawerListViewAdapter;
    private Toolbar mToolbar;

    // you only need this is you want to use the home button to toggle the nav drawer
    // else, you can call: `mDrawerLayout.openDrawer(mNavDrawerListView)` and `mDrawerLayout.closeDrawer(mNavDrawerListView)` by pressing whatever button you want. 
    private ActionBarDrawerToggle mDrawerToggle;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setContentView(R.layout.activity_main);

        setupViews();
    }

    private void setupViews() {
        mDrawerLayout = (DrawerLayout) findViewById(R.id.nav_drawer_layout);
        mNavDrawerListView = (ListView) findViewById(R.id.nav_drawer);
        mToolbar = (Toolbar) findViewById(R.id.toolbar);

        mNavDrawerListViewAdapter = new NavDrawerListViewAdapter(this, getNavDrawerItems());
        mNavDrawerListView.setAdapter(mNavDrawerListViewAdapter);
        mNavDrawerListView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                mNavDrawerListViewAdapter.setSelectedPosition(position);
                mNavDrawerListViewAdapter.notifyDataSetChanged();

                NavDrawerItemVo item = getNavDrawerItems().get(position);

                mNavDrawerListView.setItemChecked(position, true);
                replaceFragment(FRAGMENT_CONTAINER_ID, item.destinationFragment, false);

                mDrawerLayout.closeDrawer(mNavDrawerListView);
            }
        });
        mDrawerToggle = new ActionBarDrawerToggle(this, mDrawerLayout, mToolbar, R.string.drawer_open, R.string.drawer_closed) {
            @Override
            public void onDrawerClosed(View drawerView) {
                super.onDrawerClosed(drawerView);

                invalidateOptionsMenu();
            }

            @Override
            public void onDrawerOpened(View drawerView) {
                super.onDrawerOpened(drawerView);

                invalidateOptionsMenu();
            }
        };
        mDrawerLayout.addDrawerListener(mDrawerToggle);

        setSupportActionBar(mToolbar);

        getSupportActionBar().setDisplayHomeAsUpEnabled(true);
        getSupportActionBar().setHomeButtonEnabled(true);
    }

    private ArrayList<NavDrawerItemVo> getNavDrawerItems() {
        ArrayList<NavDrawerItemVo> navDrawerItems = new ArrayList<>();

        navDrawerItems.add(new NavDrawerItemVo(R.drawable.ic_foo, R.string.foo, DummyFragment.newInstance()));
        navDrawerItems.add(new NavDrawerItemVo(R.drawable.ic_bar, R.string.bar, DummyFragment.newInstance()));

        return navDrawerItems;
    }

    @Override
    protected void onPostCreate(Bundle savedInstanceState) {
        super.onPostCreate(savedInstanceState);

        mDrawerToggle.syncState();
    }

    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        super.onConfigurationChanged(newConfig);

        mDrawerToggle.onConfigurationChanged(newConfig);
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        if (mDrawerToggle.onOptionsItemSelected(item)) {
            return true;
        }

        return super.onOptionsItemSelected(item);
    }

}

```

* Create your VO object the listview adapter will take in:

```
public class NavDrawerItemVo {

    public int drawableRes;
    public int title;
    public BaseFragment destinationFragment;

    public NavDrawerItemVo(int drawableRes, int title, BaseFragment destinationFragment) {
        this.drawableRes = drawableRes;
        this.title = title;
        this.destinationFragment = destinationFragment;
    }

}
```

* Create your adapter:

```
import android.content.Context;
import android.support.v4.content.ContextCompat;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.ArrayAdapter;
import android.widget.ImageView;
import android.widget.TextView;

import java.util.ArrayList;

public class NavDrawerListViewAdapter extends ArrayAdapter<NavDrawerItemVo> {

    private Context mContext;
    private ArrayList<NavDrawerItemVo> mData;

    private int mSelectedPosition = 0;

    public NavDrawerListViewAdapter(Context context, ArrayList<NavDrawerItemVo> objects) {
        super(context, R.layout.adapter_nav_drawer_row, objects);

        mContext = context;
        mData = objects;
    }

    static class ViewHolder {
        public ImageView icon;
        public TextView title;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View rowView = convertView;

        if (rowView == null) {
            LayoutInflater inflater = (LayoutInflater) mContext.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
            rowView = inflater.inflate(R.layout.adapter_nav_drawer_row, parent, false);

            ViewHolder viewHolder = new ViewHolder();
            viewHolder.icon = (ImageView) rowView.findViewById(R.id.nav_drawer_item_icon);
            viewHolder.title = (TextView) rowView.findViewById(R.id.nav_drawer_item_title_textview);

            rowView.setTag(viewHolder);
        }

        ViewHolder viewHolder = (ViewHolder) rowView.getTag();

        final NavDrawerItemVo rowItem = mData.get(position);

        viewHolder.icon.setImageResource(rowItem.drawableRes);
        viewHolder.title.setText(rowItem.title);

        if (position == mSelectedPosition) {
            rowView.setBackgroundColor(ContextCompat.getColor(mContext, android.R.color.white));
            viewHolder.title.setTextColor(ContextCompat.getColor(mContext, R.color.navigatin_drawer_color));
        } else {
            rowView.setBackgroundColor(ContextCompat.getColor(mContext, R.color.navigatin_drawer_color));
            viewHolder.title.setTextColor(ContextCompat.getColor(mContext, android.R.color.white));
        }

        return rowView;
    }

    public void setSelectedPosition(int position) {
        mSelectedPosition = position;
    }

}
```
