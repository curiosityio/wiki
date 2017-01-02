---
name: Design support library
---

# Bottom sheets

### Implement bottom sheet

You can follow this guide here to create the view of a bottom sheet or you can create a bottom sheet fragment (docs below)

* Create/edit layout file that bottom sheet is going to live in. Your fragment, activity.

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:custom="http://schemas.android.com/apk/res-auto"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:orientation="horizontal"
    android:weightSum="1"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/white">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal">

        <!-- Put your main view content in here. -->
    </LinearLayout>

    <android.support.v4.widget.NestedScrollView
        android:id="@+id/bottom_sheet"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:clipToPadding="true"
        android:background="@android:color/white"
        app:layout_behavior="android.support.design.widget.BottomSheetBehavior">

        <TextView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:text="@string/save"
            android:padding="16dp"
            android:textSize="16sp"/>

    </android.support.v4.widget.NestedScrollView>
</android.support.design.widget.CoordinatorLayout>
```

The key parts here is the CoordinatorLayout (extends viewgroup. It takes 1 child, in this case a LinearLayout with full height/width to house the main view content, and then another child, in this case a NestedScrollView which is the bottom sheet.) and the NestedScrollView. The CoordinatorLayout is required in order to display the bottom sheet. The `app:layout_behavior` tells the CoordinatorLayout how this view should behave. Put whatever you want inside of the NestedScrollView as that is your viewgroup to have be the content of the bottom sheet.

* Setup the bottom sheet in your activity/fragment:

```
public class FooFragment extends Fragment {

    private View mBottomSheet;
    private BottomSheetBehavior mBottomSheetBehavior;
    ...
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_foo, container, false);
        ...
        mBottomSheet = view.findViewById(R.id.bottom_sheet);

        setupViews();

        return view;
    }

    private void setupViews() {
        mBottomSheetBehavior = BottomSheetBehavior.from(mBottomSheet);
    }
}
```

Bottom sheet is all setup now.

* Last step is showing the bottom sheet.

```
mBottomSheetBehavior.setState(BottomSheetBehavior.STATE_EXPANDED);
```

Call this anywhere you would like it to appear. The user can now swipe down to make it disappear or change state to `STATE_COLLAPSED` to manually do it.

### Make bottom sheet peek.

If you don't want the bottom sheet to appear in full height, you can set it to peek.

```
mBottomSheetBehavior.setPeekHeight(300);
mBottomSheetBehavior.setState(BottomSheetBehavior.STATE_COLLAPSED);
```

That is it.

Now, when the user slides the bottom sheet up it will go full height. Then from here when they slide down it will go back to it's peek size. If you want the whole bottom sheet to disappear in full every time even when not in peek mode:

```
mBottomSheetBehavior.setBottomSheetCallback(new BottomSheetBehavior.BottomSheetCallback() {
    @Override
    public void onStateChanged(View bottomSheet, int newState) {
        if (newState == BottomSheetBehavior.STATE_COLLAPSED) {
            mBottomSheetBehavior.setPeekHeight(0);
        }
    }

    @Override
    public void onSlide(View bottomSheet, float slideOffset) {
    }
});
```

### Bottom sheet fragment

* Create the fragment

```kotlin
import android.app.Dialog
import android.support.design.widget.BottomSheetDialogFragment
import android.support.design.widget.BottomSheetBehavior.BottomSheetCallback
import android.support.design.widget.BottomSheetBehavior
import android.support.annotation.NonNull
import android.support.design.widget.CoordinatorLayout
import android.view.View

class TutsPlusBottomSheetDialogFragment : BottomSheetDialogFragment() {

    private val mBottomSheetBehaviorCallback = object : BottomSheetBehavior.BottomSheetCallback() {
        override fun onStateChanged(bottomSheet: View, newState: Int) {
            if (newState == BottomSheetBehavior.STATE_HIDDEN) {
                dismiss()
            }
        }
        override fun onSlide(bottomSheet: View, slideOffset: Float) {
        }
    }

    override fun setupDialog(dialog: Dialog?, style: Int) {
        super.setupDialog(dialog, style)

        val contentView = View.inflate(context, R.layout.fragment_bottom_sheet, null)
        dialog!!.setContentView(contentView)

        val params: CoordinatorLayout.LayoutParams = (contentView.parent as View).layoutParams as CoordinatorLayout.LayoutParams
        val behavior = params.behavior

        behavior?.let { behavior ->
            (behavior as BottomSheetBehavior<*>).setBottomSheetCallback(mBottomSheetBehaviorCallback)
            behavior.state = BottomSheetBehavior.STATE_EXPANDED // <---- if you want it to *always* show up in expanded view instead of peek.
        }
    }

}
```

* In your fragment/activity you want to display the bottom sheet, use:

```
private TutsPlusBottomSheetDialogFragment mTutsPlusBottomSheetDialogFragment;
private static final String BOTTOM_SHEET_FRAG_TAG = "bottomSheetFragTag";
...
mTutsPlusBottomSheetDialogFragment = new TutsPlusBottomSheetDialogFragment();
getChildFragmentManager().beginTransaction()
                         .add(mTutsPlusBottomSheetDialogFragment, BOTTOM_SHEET_FRAG_TAG)
                         .commitAllowingStateLoss();
...
// Then when you want to dismiss it:
((TutsPlusBottomSheetDialogFragment) getChildFragmentManager().findFragmentByTag(BOTTOM_SHEET_FRAG_TAG)).dismissAllowingStateLoss();
```
