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
    private ItemClickListener mListener;

    public static class ViewHolder extends RecyclerView.ViewHolder {

        private TextView mPositionTitleTextView;

        public ViewHolder(View view) {
            super(view);

            mPositionTitleTextView = (TextView) view.findViewById(R.id.position_title_textview);
        }
    }

    public interface ItemClickListener {
        void onItemClick(int position);
    }

    public FootballScoresListAdapter(ArrayList<FootballScoreVo> data, ItemClickListener listener) {
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

        holder.mPositionTitleTextView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mListener.onItemClick(holder.getAdapterPosition());
            }
        });
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

# Simple list layout

In case you want to create a very simple layout for something like an about page, here is one. TextView takes up the whole view so you can use it as a touch listener for the layout.
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:weightSum="1"
    android:layout_height="60dp">

    <TextView
        android:id="@+id/about_list_title_textview"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:gravity="center_vertical"
        android:textSize="17sp"
        android:layout_marginLeft="19dp"
        android:textColor="@color/list_title_text_color"/>

    <View
        android:layout_width="match_parent"
        android:layout_height="1dp"
        android:background="@color/divider_color"/>

</LinearLayout>
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

# Horizontal scrolling RecyclerView

```
// populate mHorizontalList.
LinearLayoutManager layoutManager = new LinearLayoutManager(getActivity(), LinearLayoutManager.HORIZONTAL, false);        mHorizontalList.setLayoutManager(layoutManager);
// set your adapter here.
```

# Multiple types of rows

If you have a RecyclerView that you want to use multiple different layouts for different types of rows:

```
public class FooRecyclerViewAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder> {

    private static final int FOO_VIEW_TYPE = 0;
    private static final int BAR_VIEW_TYPE = 1;
    private static final int OTHER_VIEW_TYPE = 2;

    private ArrayList<FooDataModel> mData;

    public static class FooViewHolder extends RecyclerView.ViewHolder {

        private TextView mDataTextView;

        public FooViewHolder(View view) {
            super(view);

            mDataTextView = (TextView) view.findViewById(R.id.foo_data_textview);
        }
    }

    public static class BarViewHolder extends RecyclerView.ViewHolder {

        private TextView mDataTextView;
        private Button mDoneButton;

        public BarViewHolder(View view) {
            super(view);

            mDataTextView = (TextView) view.findViewById(R.id.bar_data_textview);
            mDoneButton = (Button) view.findViewById(R.id.bar_done_button);
        }
    }

    public static class OtherViewHolder extends RecyclerView.ViewHolder {

        private TextView mDataTextView;
        private Button mDoneButton;

        public OtherViewHolder(View view) {
            super(view);

            mDataTextView = (TextView) view.findViewById(R.id.other_data_textview);
            mDoneButton = (Button) view.findViewById(R.id.other_done_button);
        }
    }

    public FooRecyclerViewAdapter(ArrayList<FooDataModel> data) {
        mData = data;
    }

    @Override
    public int getItemViewType(int position) {
        FooDataModel data = mData.get(position);

        // this part may be different depending on the data type you use. Either way, return an different integer for each different row you want to represent.
        switch (data.type) {
            case "foo":
                return BULLET_POINT_VIEW_TYPE;
            case "bar":
                return SAFETY_CHECK_VIEW_TYPE;
            case "other":
                return ACKNOWLEDGEMENT_VIEW_TYPE;
            default:
                return BULLET_POINT_VIEW_TYPE;
        }
    }

    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        switch (viewType) {
            case FOO_VIEW_TYPE:
                return new FooViewHolder(LayoutInflater.from(parent.getContext()).inflate(R.layout.foo_adapter_row, parent, false));
            case BAR_VIEW_TYPE:
                return new BarViewHolder(LayoutInflater.from(parent.getContext()).inflate(R.layout.bar_adapter_row, parent, false));
            case OTHER_VIEW_TYPE:
                return new OtherViewHolder(LayoutInflater.from(parent.getContext()).inflate(R.layout.other_adapter_row, parent, false));
            default:
                return new FooViewHolder(LayoutInflater.from(parent.getContext()).inflate(R.layout.foo_adapter_row, parent, false));
        }
    }

    @Override
    public void onBindViewHolder(RecyclerView.ViewHolder holder, final int position) {
        final FooDataModel data = mData.get(position);

        switch (data.type) {
            case "foo":
                FooViewHolder fooViewHolder = (FooViewHolder) holder;

                fooViewHolder.mDataTextView.setText(data.text);
                break;
            case "bar":
                BarViewHolder barViewHolder = (BarViewHolder) holder;

                barViewHolder.mDataTextView.setText(data.text);
                break;
            case "other":
                OtherViewHolder otherViewHolder = (OtherViewHolder) holder;

                otherViewHolder.mDataTextView.setText(data.text);
                break;
            default:
                break;
        }
    }

    @Override
    public int getItemCount() {
        return mData.size();
    }

}
```

# Disable overscroll glow

```
android:overScrollMode="never"
```

# Add drag and drop functionality to RecyclerViews

Original guide to walk through how to do this [part 1](https://medium.com/@ipaulpro/drag-and-swipe-with-recyclerview-b9456d2b1aaf#.u3c14avod) and [part 2](https://medium.com/@ipaulpro/drag-and-swipe-with-recyclerview-6a6f0c422efd#.643t6ed6m) (I created local copies in project for it in misc-docs. Also guides help setup swiping.)

* ItemTouchHelper is a special class in the RecyclerView support library to help you do swipe to dismiss and drag and drop. We will use that here.

```
import android.support.v7.widget.RecyclerView;
import android.support.v7.widget.helper.ItemTouchHelper;

public class DragAndDropRecyclerViewCallback extends ItemTouchHelper.Callback {

    public interface ItemTouchHelperListener {

        void onItemMove(int fromPosition, int toPosition);
    }

    private ItemTouchHelperListener mListener;

    public DragAndDropRecyclerViewCallback(ItemTouchHelperListener listener) {
        mListener = listener;
    }

    @Override
    public boolean isLongPressDragEnabled() {
        return false;
    }

    @Override
    public boolean isItemViewSwipeEnabled() {
        return false;
    }

    @Override
    public void onSwiped(RecyclerView.ViewHolder viewHolder, int direction) {
    }

    @Override
    public int getMovementFlags(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder) {
        int dragFlags = ItemTouchHelper.UP | ItemTouchHelper.DOWN;

        return makeFlag(ItemTouchHelper.ACTION_STATE_DRAG, dragFlags);
    }

    @Override
    public boolean onMove(RecyclerView recyclerView, RecyclerView.ViewHolder viewHolder, RecyclerView.ViewHolder target) {
        mListener.onItemMove(viewHolder.getAdapterPosition(), target.getAdapterPosition());

        return true;
    }

}
```

* Make your RecyclerView adapter implement this ItemTouchHelper interface ItemTouchHelperListener created above:

```
public class FooListAdapter extends RecyclerView.Adapter<FooListAdapter.ViewHolder> implements DragAndDropRecyclerViewCallback.ItemTouchHelperListener {

    public interface DragReorderListener {
        void onStartReorderDrag(RecyclerView.ViewHolder viewHolder);
    }

    private List<FooData> mData;
    private List<FooData> mOriginalStateData;

    private DragReorderListener mDragReorderListener;

    private Context mContext;

    public static class ViewHolder extends RecyclerView.ViewHolder {

        public TextView title;
        public ImageView dragableHandleImageView;

        public ViewHolder(View view) {
            super(view);

            title = (TextView) view.findViewById(R.id.foo_textview);
            dragableHandleImageView = (ImageView) view.findViewById(R.id.dragable_handle_imageview);
        }
    }

    public FooListAdapter(Context context, List<FooData> data) {
        mContext = context;
        mData = data;
        mOriginalStateData = new ArrayList<>(mData);
    }

    // if user moves the position of an item, the original data is modified. Here, I restore the original data that is unmodified.
    public void restoreToOriginalState() {
        mData = new ArrayList<>(mOriginalStateData);

        notifyDataSetChanged();        
    }

    public void setDragReorderListener(DragReorderListener dragReorderListener) {
        mDragReorderListener = dragReorderListener;
    }

    @Override
    public ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.adapter_foo_adapter, parent, false);

        return new ViewHolder(view);
    }

    @Override
    public void onBindViewHolder(final ViewHolder viewHolder, final int inaccuratePosition) {
        final int position = viewHolder.getAdapterPosition();
        final FooData rowItem = mData.get(position);

        ...

        viewHolder.dragableHandleImageView.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                if (MotionEventCompat.getActionMasked(event) == MotionEvent.ACTION_DOWN) {
                    mDragReorderListener.onStartReorderDrag(viewHolder);
                }

                return false;
            }
        });
    }

    @Override
    public void onItemMove(int fromPosition, int toPosition) {
        Collections.swap(mData, fromPosition, toPosition);

        notifyItemMoved(fromPosition, toPosition);
    }

}
```

*Note: The imageview image for reordering can be found in the Android Material Design icon set.*

* Then in your fragment where you have your RecyclerView:

```
public class FooFragment extends Fragment implements FooListAdapter.DragReorderListener {

    private ItemTouchHelper mDragAndDropRecyclerViewTouchHelper;

    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        ...
        mFooRecylerView = (RecyclerView) view.findViewById(R.id.foo_recyclerview);

        setupViews();

        return view;
    }

    private void setupViews() {
        mFooRecyclerViewAdapter = new FooRecyclerViewAdapter(getActivity(), mData);
        mProcessStepsListAdapter.setDragReorderListener(this);

        DragAndDropRecyclerViewCallback callback = new DragAndDropRecyclerViewCallback(mFooRecyclerViewAdapter);
        mDragAndDropRecyclerViewTouchHelper = new ItemTouchHelper(callback);
        mDragAndDropRecyclerViewTouchHelper.attachToRecyclerView(mFooRecylerView);

        mFooRecylerView.setLayoutManager(new LinearLayoutManager(getActivity()));
        mFooRecylerView.setAdapter(mFooRecyclerViewAdapter);
    }

    @Override
    public void onStartReorderDrag(RecyclerView.ViewHolder viewHolder) {
        mDragAndDropRecyclerViewTouchHelper.startDrag(viewHolder);
    }

}
```

### Error: Inconsistency detected. Invalid item position 2(offset:2).state:3

This exception may happen to you in your RecyclerView adapter when you try to refresh the RecyclerView. When this issue happened in my app, I was using `notifyItemRangeChanged(0, mData.size());` and the error was fixed when I instead called `notifyDataSetChanged()`. There are times when to use each.
