---
name: Random Android stuff that doesn't deserve it's own file
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
