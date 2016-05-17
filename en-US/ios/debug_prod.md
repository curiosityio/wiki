---
name: Debug and Prod build versions
---

# Change code according to debug or release

```
#if !DEBUG
    static let hostname: String = "https://api.curiosityio.com"
#else
    static let hostname: String = "https://staging.curiosityio.com"
#endif
```

This works because it is a XCode preprocessor macro. This will not work by default. You must add the custom flag to the Swift compiler custom flags in your Build Settings.
Go to  Build Settings -> Swift Compiler - Custom Flags then add `-DDEBUG` as an entry for Debug:

![](/docs/images/swift_custom_debug_flag.png)

[Original help](http://stackoverflow.com/a/24112024/1486374)
