---
name: Files
---

# Read from raw file

This works great when you have a JSON file locally you want to read.

* Create files in `app/src/main/res/raw/` directory. Can be any file type.

* In Activity or Application, using the resources, this is how you would easily read a JSON file using Gson and Kotlin:

```
class TwitterCreds(var key: String = "", var secret: String = "")

override fun onCreate() {
    super.onCreate()

    val creds: TwitterCreds = Gson().fromJson(resources.openRawResource(R.raw.twittercreds).bufferedReader().use { it.readText() }, TwitterCreds::class.java)
}
```

The code above reads the file `twittercreds.json` and parses it to the TwitterCreds class. 
