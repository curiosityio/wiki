---
name: GridView
---

# Implement GridView

* Create GridView in layout file

```
<GridView
    android:id="@+id/foo_gridview"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:columnWidth="110dp" <!-- or auto_fit to fit as many as possible with adapter root -->
    android:numColumns="2"
    android:verticalSpacing="10dp"
    android:horizontalSpacing="10dp"
    android:stretchMode="columnWidth"
    android:gravity="center"/>
```

* Create adapter:

```
package com.howfactory.howfactory.adapter.gridview;

import android.content.Context;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.CheckBox;
import android.widget.GridView;
import android.widget.ImageView;
import android.widget.TextView;

import java.util.ArrayList;

public class FooAdapter extends BaseAdapter {

    private Context mContext;
    private ArrayList<FooVo> mData;

    private ItemClickListener mListener;

    public FooAdapter(Context context, ArrayList<FooVo> data, ItemClickListener listener) {
        mContext = context;
        mData = data;
        mListener = listener;
    }

    public interface ItemClickListener {
        void onItemClick(int position);
    }

    public int getCount() {
        return mData.size();
    }

    public Object getItem(int position) {
        return null;
    }

    public long getItemId(int position) {
        return 0;
    }

    public View getView(int position, View convertView, ViewGroup parent) {
        View view = convertView;
        ImageView imageView;

        if (view == null) {
            view = LayoutInflater.from(mContext).inflate(R.layout.adapter_foo, parent, false);
            view.setTag(R.id.imageview, view.findViewById(R.id.imageview));
        }

        FooVo data = mData.get(position);

        imageView = (ImageView) view.getTag(R.id.imageview);

        Glide.with(imageView.getContext())
             .load(answer.image)
             .asBitmap()
             .placeholder(R.drawable.ic_placeholder)
             .into(imageView);

        convertView.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mListener.onItemClick(data);
            }
        });

        return view;
    }

}
```

* Set adapter:

```
GridView fooGridView = (GridView) view.findViewById(R.id.foo_gridview);
fooGridView.setAdapter(new FooAdapter(getActivity(), data, this));
```

# Full height non-scrollable GridView

GridViews are awesome with displaying a grid of items. However, if you want to make them not scrollable so you can embed it in a scrollview with views below and above it, then use this.

```
package foo.bar;

import android.content.Context;
import android.util.AttributeSet;
import android.view.ViewGroup;
import android.widget.GridView;

// credits: http://stackoverflow.com/a/8483078/1486374 
public class ExpandedHeightGridView extends GridView {

    boolean expanded = true;

    public ExpandedHeightGridView(Context context) {
        super(context);
    }

    public ExpandedHeightGridView(Context context, AttributeSet attrs) {
        super(context, attrs);
    }

    public ExpandedHeightGridView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
    }

    public boolean isExpanded() {
        return expanded;
    }

    @Override
    public void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        // HACK! TAKE THAT ANDROID!
        if (isExpanded()) {
            // Calculate entire height by providing a very large height hint.
            // View.MEASURED_SIZE_MASK represents the largest height possible.
            int expandSpec = MeasureSpec.makeMeasureSpec(MEASURED_SIZE_MASK, MeasureSpec.AT_MOST);
            super.onMeasure(widthMeasureSpec, expandSpec);

            ViewGroup.LayoutParams params = getLayoutParams();
            params.height = getMeasuredHeight();
        } else {
            super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        }
    }

    public void setExpanded(boolean expanded) {
        this.expanded = expanded;
    }

}
```

* Use in layout:

```
<com.example.ExpandableHeightGridView
    android:id="@+id/myId"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:gravity="center"
    android:horizontalSpacing="2dp"
    android:isScrollContainer="false"
    android:numColumns="4"
    android:stretchMode="columnWidth"
    android:verticalSpacing="20dp" />
```

This is exactly like a normal GridView.

* Use in java code:

```
mAppsGrid = (ExpandableHeightGridView) findViewById(R.id.myId);
mAppsGrid.setExpanded(true);
```
