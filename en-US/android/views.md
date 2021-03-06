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

# Return/enter key listener EditText

```
FooEditText.setOnKeyListener { view, keyCode, event ->
    if ((event.action == KeyEvent.ACTION_DOWN) && (keyCode == KeyEvent.KEYCODE_ENTER)) {
        // enter key hit
        true
    } else {
        false
    }
}
```

# Make ImageView act more like a button

You can add click listeners to imageviews to make them a button but when you press them, there is no visual effect.

Add this to your ImageView's view:

```
android:background="?attr/selectableItemBackgroundBorderless"
android:clickable="true"
```

# Center crop image in ImageView

Set the ScaleType to an ImageView. Easiest way is via XML:

```
<ImageView
    android:layout_width="match_parent"
    android:layout_height="10dp"
    android:scaleType="centerCrop"/>
```

# Add border around ImageView

If you want to add a border around your ImageView to give it UI as though it is selected:

* Create a drawable to be the border:

```
<?xml version="1.0" encoding="UTF-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <stroke
        android:width="4dp"
        android:color="@android:color/white" />
    <padding
        android:left="3dp"
        android:top="3dp"
        android:right="3dp"
        android:bottom="3dp" />
</shape>
```

* Set the drawable border to the ImageView:

```
if (addBorderToImageView) {
    imageView.setBackgroundResource(R.drawable.border_imageview_drawable_created_above)
} else {
    imageView.setBackgroundResource(0) // remove the border
}
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

# Material design EditText with floating label

```
<android.support.design.widget.TextInputLayout
    android:id="@+id/text_input_layout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content">

    <android.support.design.widget.TextInputEditText
        android:id="@+id/editText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="UserName"/>
</android.support.design.widget.TextInputLayout>
```

### Style floating label color

```
<android.support.design.widget.TextInputLayout
    android:id="@+id/text_input_layout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    app:hintTextAppearance="@style/WhiteTextColorFloatingEditText"> <!-- Add this line -->

    <android.support.design.widget.TextInputEditText
        android:id="@+id/editText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textColor="@android:color/white" <---- text color of EditText.
        android:theme="@style/WhiteTextTextInputEditText" <---- Set this for underline color of EditText on focused state.
        android:hint="UserName"/>
</android.support.design.widget.TextInputLayout>
```

Then, in your styles.xml file, add this style:

```
<resources>
    ...
    <style name="WhiteTextTextInputEditText" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="android:textColorHint">@android:color/white</item>
        <item name="colorAccent">@android:color/white</item>
        <item name="colorControlNormal">@android:color/white</item>
        <item name="colorControlActivated">@android:color/white</item>
    </style>        

    <style name="WhiteTextColorFloatingEditText" parent="TextAppearance.AppCompat">
        <!-- Hint color and label color in FALSE state -->
        <item name="android:textColorHint">@android:color/white</item>
        <item name="android:textSize">20sp</item> <!-- if you want it......I leave it out to default. -->
        <!-- Label color in TRUE state and bar color FALSE and TRUE State -->
        <item name="colorAccent">@android:color/white</item>
        <item name="colorControlNormal">@android:color/white</item>
        <item name="colorControlActivated">@android:color/white</item>
    </style>

</resources>
```

And enter in whatever colors you wish. Thank you to [this SO answer](http://stackoverflow.com/a/30914037/1486374) for the help.

# Set capitalization of EditText

```
fun EditText.passwordInputType() {
    inputType = InputType.TYPE_CLASS_TEXT or InputType.TYPE_TEXT_VARIATION_PASSWORD
}

fun EditText.emailInputType() {
    inputType = InputType.TYPE_CLASS_TEXT or InputType.TYPE_TEXT_VARIATION_EMAIL_ADDRESS
}

fun EditText.normalTextInputType() {
    inputType = InputType.TYPE_CLASS_TEXT or InputType.TYPE_TEXT_FLAG_CAP_SENTENCES
}

fun EditText.allCapsTextInputType() {
    inputType = InputType.TYPE_CLASS_TEXT or InputType.TYPE_TEXT_FLAG_CAP_CHARACTERS
}
```
