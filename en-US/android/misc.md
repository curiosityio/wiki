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
