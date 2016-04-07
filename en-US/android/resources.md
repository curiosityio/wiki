---
name: Android resources
---

# Get color from color resource

If *used to be* this way
```
getResources().getColor(R.color.idname);
```
But it got changed to:
```
ContextCompat.getColor(context, R.color.your_color)
```
