---
name: RecyclerViews
---

# Create RecyclerView for screen in app

* Add RecyclerView to layout file:
```
<android.support.v7.widget.RecyclerView
    android:id="@+id/football_scores_recycler_view"
    android:scrollbars="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>
```

* Create adapter for RecyclerView:
```
import android.support.v7.widget.RecyclerView;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.Button;
import android.widget.ImageButton;
import android.widget.TextView;

import java.util.ArrayList;

public class FootballScoresListAdapter extends RecyclerView.Adapter<FootballScoresListAdapter.ViewHolder> {

    private ArrayList<FootballScoreVo> mData;
    private AdapterListener mListener;

    public static class ViewHolder extends RecyclerView.ViewHolder {

        private TextView mPositionTitleTextView;

        public ViewHolder(View view) {
            super(view);

            mPositionTitleTextView = (TextView) view.findViewById(R.id.position_title_textview);
        }
    }

    public interface AdapterListener {
        void openButtonPressed(int position, FootballScoreVo data);
    }

    public FootballScoresListAdapter(ArrayList<FootballScoreVo> data, AdapterListener listener) {
        mData = data;
        mListener = listener;
    }

    @Override
    public FootballScoresListAdapter.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.football_scores_list_row, parent, false);

        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(ViewHolder holder, final int position) {
        final FootballScoreVo data = mData.get(position);

        holder.mPositionTitleTextView.setText(data.title);
    }

    @Override
    public int getItemCount() {
        return mData.size();
    }

}
```

* Set this adapter to the RecyclerView in the activity/fragment:
```
mFootballScoresListAdapter = new InboxListAdapter(mScores, this);
mFootballScoresList.setLayoutManager(new LinearLayoutManager(getActivity()));
mFootballScoresList.setAdapter(mFootballScoresListAdapter);
```

# Pull to refresh ability

* Wrap RecyclerViews in your layout files:
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <android.support.v4.widget.SwipeRefreshLayout
        android:id="@+id/swipe_refresh_list"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <android.support.v7.widget.RecyclerView
            android:id="@+id/recycler_view"
            android:scrollbars="vertical"
            android:layout_width="match_parent"
            android:layout_height="match_parent"/>

    </android.support.v4.widget.SwipeRefreshLayout>

    <com.levibostian.view.ListEmptyView
        android:id="@+id/list_empty_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>

</RelativeLayout>
```
See the android.support.v4.widget.SwipeRefreshLayout? Wrap your RecyclerView in that. Also notice how I have a custom view empty view here? That will come into play soon.

This is all we have to do to add the widget and pull ability. Your user can now pull down and see the widget spin around infinitely.

* Create listener to listen for refresh events. In your activity/fragment wherever you have your RecyclerView:
```
mSwipeRefreshLayout = (SwipeRefreshLayout) findViewById(R.id.swipe_refresh_list); // <-- id of SwipeRefreshLayout we set above in layout file.
...
mSwipeRefreshLayout.setOnRefreshListener(new SwipeRefreshLayout.OnRefreshListener() {
    @Override
    public void onRefresh() {
        // do our refresh of data.
        // When complete, call:
        mSwipeRefreshLayout.setRefreshing(false);
    }
});
```

* For accessibility reasons, add a refresh item to your actionbar as well. I add mine as a menu item that is always hidden:
```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <item android:id="@+id/refresh"
        android:title="@string/refresh"
        app:showAsAction="never"/>

    ...

</menu>
```
If you forgot how to listen for actionbar menu item clicks, check out the [android menu docs]('menus').

* Done. One side note: SwipeRefreshLayout only works for 1 child and that child must be a GridView, ListView, RecyclerView, ScrollView (I think ScrollView?). Here, in this layout file, we have an empty view. If our RecyclerView is empty and doesn't have data, we show the empty view:
```
private void showListOrEmptyView() {
    if (mFootballScoresData.size() <= 0) {
        mEmptyView.setVisibility(View.VISIBLE);
    } else {
        mEmptyView.setVisibility(View.GONE);
    }
}
```
Notice how we did not change visibility of the RecyclerView. If we set it to INVISIBLE or GONE, then when you try to pull down, the widget will not appear or work. So keep the visibility of it visible at all times and just change visibility of the empty view.
