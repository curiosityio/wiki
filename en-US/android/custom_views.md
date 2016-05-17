---
name: Android custom views
---

Custom views are Views, LinearLayouts, Spinners, etc. You can take any existing view or viewgroup from Android and modify them to do whatever you want. This is awesome because it packages everything up nice into one re-useable view that you can use in other places in your app.

# Create custom view

* Create new Java file to hold your custom view code:
```
package com.levibostian.view;

import android.annotation.TargetApi;
import android.content.Context;
import android.os.Build;
import android.util.AttributeSet;
import android.view.LayoutInflater;
import android.widget.LinearLayout;
import android.widget.TextView;
import com.levibostian.app.R;

public class ListEmptyView extends LinearLayout {

    private TextView mStatusTextView;

    public ListEmptyView(Context context, AttributeSet attrs) {
        this(context, attrs, 0);
    }

    public ListEmptyView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);

        initialize(context, attrs, defStyleAttr);
    }

    @TargetApi(Build.VERSION_CODES.LOLLIPOP)
    public ListEmptyView(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);

        initialize(context, attrs, defStyleAttr);
    }

    private void initialize(Context context, AttributeSet attrs, int defStyleAttr) {
        LayoutInflater.from(context).inflate(R.layout.view_list_empty, this, true);

        mStatusTextView = (TextView) findViewById(R.id.status_textview);
        // do stuff with mStatusTextView if you would like.
    }

}
```
Notice that I am extending LinearLayout. Anything and everything that a LinearLayout does, this class will do too.

* Create the `R.layout.view_list_empty` file:
```
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center">

    <TextView
        android:id="@+id/status_textview"
        android:layout_height="wrap_content"
        android:textSize="18sp"
        android:gravity="center"
        android:textColor="@color/empty_view_text_color"
        tools:text="Yo."/>

</merge>
```
The `<merge>` tags are there for a reason. This means to take this layout, remove the parent layout (the `<merge>` stuff) and merge that into our custom view. So pretty much when this view is inflated into our custom view, merge means "turn me into a LinearLayout because the custom view I am being inflated into is a LinearLayout". This brings up a good point. See the `android:gravity="center"` part there in the <merge> tag? That will actually not work. <merge> will remove all special attributes in there. So if you want to do things such as set the orientation or gravity, you have to do that in Java code:
```
setOrientation(VERTICAL);
setGravity(Gravity.CENTER);
```

* When you want to use this custom view in your other layouts:
```
<com.levibostian.view.ListEmptyView
    android:layout_width="match_parent"
    android:layout_height="match_parent"/>
```
Just include it anywhere like it was any other view.

# Customizing custom views through custom view attributes

Customizing custom views in XML is very handy. You can make a "list empty view" custom view that is generic and anytime you want to use it in your XML layout, you can customize it like this:
```
<com.levibostian.view.ListEmptyView
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    custom:empty_text="You have no messages in your inbox."/>
```
Then our custom view will change it's text to "You have no messages in your inbox."

* To define custom attributes, add `<declare-styleable>` resources to your project. It's customary to put these resources into a `res/values/attrs.xml` file. Here's an example of an attrs.xml file:
```
<resources>
   <declare-styleable name="ListEmptyView">
       <attr name="empty_text" format="string"/>
       <attr name="showText" format="boolean" />
       <attr name="labelPosition" format="enum">
           <enum name="left" value="0"/>
           <enum name="right" value="1"/>
       </attr>
   </declare-styleable>
</resources>
```
* In the layout files you are using this custom view you can use these custom attributes now:
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   xmlns:custom="http://schemas.android.com/apk/res/com.example.customviews">

 <com.levibostian.view.ListEmptyView
     custom:showText="true"
     custom:labelPosition="left"
     custom:empty_text="Yo. You are empty."/>

</LinearLayout>
```
* Apply the custom attributes in your custom view Java code:
```
package com.levibostian.view;

import android.annotation.TargetApi;
import android.content.Context;
import android.os.Build;
import android.util.AttributeSet;
import android.view.LayoutInflater;
import android.widget.LinearLayout;
import android.widget.TextView;
import com.levibostian.app.R;

public class ListEmptyView extends LinearLayout {

    private TextView mStatusTextView;

    public ListEmptyView(Context context, AttributeSet attrs) {
        this(context, attrs, 0);
    }

    public ListEmptyView(Context context, AttributeSet attrs, int defStyleAttr) {
        this(context, attrs, defStyleAttr, 0);
    }

    @TargetApi(Build.VERSION_CODES.LOLLIPOP)
    public ListEmptyView(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);

        initialize(context, attrs, defStyleAttr);
    }

    private void initialize(Context context, AttributeSet attrs, int defStyleAttr) {
        LayoutInflater.from(context).inflate(R.layout.view_list_empty, this, true);

        mStatusTextView = (TextView) findViewById(R.id.status_textview);
        // do stuff with mStatusTextView if you would like.

        TypedArray a = context.getTheme().obtainStyledAttributes(attrs, R.styleable.ListEmptyView, 0, 0);

        try {
            mShowText = a.getBoolean(R.styleable.ListEmptyView_showText, false);
            mTextPos = a.getInteger(R.styleable.ListEmptyView_labelPosition, 0);

            String statusText = a.getString(R.styleable.ListEmptyView_empty_text);
            if (statusText != null) {
                mStatusTextView.setText();    
            }        
        } finally {
            a.recycle();
        }
    }

}
```

*Note: You must pull out the custom attributes in the initialize() function. The custom attributes get thrown away if you try to retrieve them anywhere else in the lifecycle of the view. If you need to use them later on, save them to a variable and use later.*

# Dynamic changing of properties of custom view

Once the system lays out and draws a custom view, it is complete. Here is how you can dynamically change it and change the text of a custom view dynamically for example:

* In your custom view Java code, add functions for others to set properties on your view then call invalidate() and requestLayout().
```
...
public void setText(String text) {
   mText = text;

   invalidate();
   requestLayout();
}
...
```

# Access children views

ViewGroups can contain children views that you can access in your custom view.

In your XML:

```
<com.levibostian.view.CustomView
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    ... children views in here such as TextViews, EditTexts, Views, or other ViewGroups.

<com.levibostian.view.CustomView/>
```

In order to access these child views, use the functions: `getChildAt()`, `getChildCount()`. However, you must access them later on then in the constructor because your child views are not inflated yet and `getChildAt()` will return null and `getChildCount()` will return 0.

```
@Override
protected void onFinishInflate() {
    super.onFinishInflate();

    // optional. If you want to add restrictions you can.
    if (getChildCount() > 1) {
        throw new RuntimeException(getClass().getSimpleName() + " cannot have more then 1 child view.");
    }

    if (getChildCount() == 0) {
        throw new RuntimeException("You forgot to add a child view to " + getClass().getSimpleName());
    }

    mContentView = getChildAt(0);
}
```

# Add views programmatically to view

```
// you can add this in your constructor or `onFinishInflate()` in the custom view.
mNewView = new NewView(mContext, mAttrs, mDefStyleAttr); // these are saved from the viewgroup constructor.
mNewView.setLayoutParams(new LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, ViewGroup.LayoutParams.MATCH_PARENT));
addView(mNewView);
```
