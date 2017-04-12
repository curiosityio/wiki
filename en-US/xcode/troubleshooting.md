---
name: Troubleshooting
---

# Autocomplete not working well

XCode has great autocomplete functionality. Sometimes, it seems sluggish and you need to force a Build in order for XCode to give you any help at all with your code.

This is what I have done to fix the issue:

* Do a clean. Product > Clean.

* Delete derived data of app. [Help found](http://stackoverflow.com/a/39495772/1486374)

You can go to File > Workspace Settings if you are in a workspace environment or File > Project Settings for a regular project environment. Then click over the little grey arrow under Derived data section and select your project folder to delete it.

* Close XCode, restart it.

---

I recently switched to using Vivaldi web browser instead of Google Chrome. Since then, XCode seems to be working much better now. I wonder if Chrome was taking too much CPU/RAM power from the system and XCode went into a low power state?

So, XCode could also be having issues if your machine is running too many intensive programs. 
