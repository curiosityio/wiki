---
name: Dialog
---

# Style dialog

* In your theme styles.xml file create a theme and set it globally across app:

```
<resources>

    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <item name="colorPrimary">@color/primary</item>
        <item name="colorPrimaryDark">@color/primary_darker</item>
        <item name="colorAccent">@color/accent</item> <!-- colors widgets such as button, edittext, checkbox colors -->        

        <!-- this line is to set theme of dialogs across entire app. -->
        <item name="alertDialogTheme">@style/AlertDialogTheme</item>
    </style>

    <style name="AlertDialogTheme" parent="Theme.AppCompat.Light.Dialog.Alert">
        <!-- title and message text color -->
        <item name="android:textColorPrimary">@android:color/black</item>
        <!-- Button color -->
        <item name="colorAccent">@color/accent</item>
        <!-- Used for the background color -->
        <item name="android:background">@android:color/white</item>
    </style>

</resources>
```

* If you want to set the theme of 1 singe dialog, create the style in styles.xml again like above, but then tell the `AlertDialog.Builder` what theme to use:

```
AlertDialog.Builder builder = new AlertDialog.Builder(this, R.style.AlertDialogTheme);
builder.setTitle("AppCompatDialog");
builder.setMessage("Lorem ipsum dolor...");
builder.setPositiveButton("OK", null);
builder.setNegativeButton("Cancel", null);
builder.show();
```

[Credit for help](http://stackoverflow.com/a/29798616/1486374)

# Custom view dialog

Use a DialogFragment.

```
class FooDialogFragment : DialogFragment() {

    interface ActionListener {
        fun fooButtonClicked()
    }

    var actionListener: ActionListener? = null

    private lateinit var fooButton: Button

    override fun onCreateDialog(savedInstanceState: Bundle?): Dialog {
        val builder = AlertDialog.Builder(activity)

        val inflater = activity.layoutInflater
        val view = inflater.inflate(R.layout.fragment_foo_dialog, null)
        builder.setView(view)

        fooButton = view.findViewById(R.id.fragment_foo_dialog_foo_button)!! as Button
        tryAgainButton.setOnClickListener { actionListener?.fooButtonClicked() }

        return builder.create()
    }

    fun updateFooButtonText(text: String) {
        fooButton.text = text
    }

}
```
