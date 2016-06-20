---
name: Keychain
---

The iOS keychain is a secure encrypted storage for your app to access.

# Delete keychain

When your app is uninstalled your app data including NSUserDefaults are all deleted however the keychain is not.

Not only that, but you cannot just "delete all keychain data". You must delete each keychain entry one by one.

I use the library [LockSmith](https://github.com/matthewpalmer/Locksmith) for maintaining my keychain.

```
try! Locksmith.deleteDataForUserAccount(email)
```

The variable `email` holds the value to the key that I used to create a keychain entry so I will use that key again to retrieve and delete this keychain entry.

Do the above for all keys in your keychain. 
