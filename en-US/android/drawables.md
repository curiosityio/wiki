---
name: Drawables
---

# Examples

* Grey rectangle
```
<?xml version="1.0" encoding="UTF-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
    <solid android:color="#CCCCCC"/>
</shape>
```

You can now add a border:
```
<?xml version="1.0" encoding="UTF-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
    <solid android:color="#CCCCCC"/>
    <stroke android:width="1px" android:color="@color/blue"/>
</shape>
```

You can add some rounded corners:
```
<?xml version="1.0" encoding="UTF-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
    <solid android:color="#CCCCCC"/>
    <stroke android:width="1px" android:color="@color/blue"/>
    <corners android:radius="20dp"/>
</shape>
```

You can add some padding to the border:
```
<?xml version="1.0" encoding="UTF-8"?>
<shape xmlns:android="http://schemas.android.com/apk/res/android" android:shape="rectangle">
    <solid android:color="#CCCCCC"/>
    <stroke android:width="1px" android:color="@color/blue"/>
    <padding android:left="10dp" android:right="10dp" android:top="10dp" android:bottom="10dp"/>
    <corners android:radius="20dp"/>
</shape>
```
