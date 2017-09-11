---
name: Intent
---

# View file in device app via intent from absolute file path

To make this easy, I use a Kotlin extension for strings. Call this on your absolute path string to get the Intent.

```
import android.app.Activity
import android.content.Context
import android.content.Intent
import android.support.v4.app.ShareCompat
import android.support.v4.content.FileProvider
import android.webkit.MimeTypeMap
import com.curiosityio.androidboilerplate.extensions.getFileExtensionWithoutDot
import java.io.File

fun String.getShareIntentForFile(context: Activity): Intent {
    val mimeTypeMap = MimeTypeMap.getSingleton()
    val mimeType = mimeTypeMap.getMimeTypeFromExtension(this.getFileExtensionWithoutDot())

    val nameOfApp = context.packageName
    val uri = FileProvider.getUriForFile(context, "$nameOfApp.fileprovider", File(this))

    return ShareCompat.IntentBuilder.from(context)
            .setStream(uri) // uri from FileProvider
            .setType(mimeType)
            .intent
            .setAction(Intent.ACTION_VIEW) //Change if needed
            .setDataAndType(uri, mimeType)
            .addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION)
}
```
