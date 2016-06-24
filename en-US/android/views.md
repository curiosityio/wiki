---
name: Various info about Android widgets and views.
---

# Close keyboard when hitting return button with EditText

`imeOptions` in layout.

```
<EditText
    android:id="@+id/email_address_edittext"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:imeOptions="actionDone"/>
```

# Make ImageView act more like a button

You can add click listeners to imageviews to make them a button but when you press them, there is no visual effect.

Add this to your ImageView's view:

```
android:background="?attr/selectableItemBackgroundBorderless"
android:clickable="true"
```

# Display HTML in TextView

TextViews can display HTML text with even hyperlinks.

```
mFooTextView.setText(Html.fromHtml(htmlString));
mFooTextView.setMovementMethod(LinkMovementMethod.getInstance());
```

Automatically opens browser if press on a hyperlink in textview.

# Checkbox

## Set color checkbox

```
<CheckBox
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:buttonTint="@color/CHECK_COLOR" />
```

You can also do this using AppCompatCheckBox v7 for older APIs:
```
<android.support.v7.widget.AppCompatCheckBox
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:buttonTint="@color/COLOR_HERE" />
```

This sets the outline color of the checkbox and the color of it filled in.

# ScrollView

## ScrollView minimum height of full height of screen.

The 1 child of a ScrollView has to have a height of wrap_content. If that 1 child has a height less then the full height of the screen, your view might look bad.

There is a trick to making your ScrollView have a minimum height of the full height of the screen and if over the full height, it acts as a normal ScrollView.

```
<?xml version="1.0" encoding="utf-8"?>
<ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_margin="12dp"
    android:fillViewport="true"> <------------ tells scrollview to occupy full screen

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:weightSum="1"> <-------------- scrollview can only occupy full screen IF the child is designed to fill the whole screen.

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content" />

        <View <------------- this dummy view will fill in the cracks of the parent LinearLayout to make it be full screen.
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:layout_weight="1"/>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="horizontal"
            android:weightSum="1">

            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                style="?android:attr/borderlessButtonStyle" />

            <View
                android:layout_width="0dp"
                android:layout_height="0dp"
                android:layout_weight="1"/>

            <Button
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                style="?android:attr/borderlessButtonStyle" />
        </LinearLayout>
    </LinearLayout>
</ScrollView>
```

Telling ScrollView to fillViewport as well as making the LinearLayout child setup to occupy an entire screen make it work. 
