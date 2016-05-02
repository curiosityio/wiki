---
name: Realm
---

# Delete database

```
mRealm.executeTransaction(new Realm.Transaction() {
    @Override
    public void execute(Realm realm) {
        realm.deleteAll();
    }
});
```
