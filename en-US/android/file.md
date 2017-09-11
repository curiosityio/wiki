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

# Download remote file and save to device

```
fun downloadAndSaveDocumentFile(context: Context, documentRemoteUrl: String?): Single<String?> {
    return Single.create { subscriber ->
        if (documentRemoteUrl == null) {
            subscriber.onSuccess(null)
        } else {
            val client = OkHttpClient()
            val request = Request.Builder().url(documentRemoteUrl).build()
            val response: Response = client.newCall(request).execute()

            if (!response.isSuccessful) {
                LogUtil.error(RuntimeException(response.message()))
                subscriber.onError(RuntimeException("Error saving document."))
            } else {
                val inputStream = response.body().byteStream()
                val nameOfApp = context.packageName.split(".").last()
                val directoryToSaveFiles = File("${context.filesDir.absolutePath}/Documents/$nameOfApp")
                directoryToSaveFiles.mkdirs()

                val fileName: String = SimpleDateFormat("yyyyMMdd_HHmmss", Locale.getDefault()).format(Date())
                val docFile: File = File(directoryToSaveFiles, fileName + documentRemoteUrl.getFileExtension()!!)

                if (!docFile.createNewFile()) {
                    subscriber.onError(RuntimeException("Error saving document. Try again."))
                    return@create
                }

                val fileAbsolutePath = docFile.absolutePath
                var outputStream: OutputStream? = null
                try {
                    outputStream = FileOutputStream(docFile)

                    val bytes = ByteArray(1024)
                    var read = inputStream.read(bytes)
                    while (read != -1) {
                        outputStream.write(bytes, 0, read)
                        read = inputStream.read(bytes)
                    }

                    subscriber.onSuccess(fileAbsolutePath)
                } catch (e: IOException) {
                    LogUtil.error(e)
                    subscriber.onError(RuntimeException("Error saving document."))
                } finally {
                    try {
                        inputStream.close()
                        // outputStream?.flush()
                        outputStream?.close()
                    } catch (e: IOException) {
                        LogUtil.error(e)
                        subscriber.onError(RuntimeException("Error saving document."))
                    }
                }
            }
        }
    }
}

// with string extension code relies on. 
fun String.getFileExtension(): String? { // returns ".png" for "yo.png"
    var extension: String? = null

    val i = lastIndexOf('.')
    if (i > 0) {
        extension = substring(i)
    }

    return extension
}
```

Make sure to call this from a background thread!
