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
