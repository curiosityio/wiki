---
name: Bitmap
---

# Take bitmap screenshot

```
private void getBitmapScreenshot() {
    //create save path to for file
    String mPath = Environment.getExternalStorageDirectory().toString() + "/" + IMAGE_FILENAME;

    //create bitmap screenshot
    Bitmap bitmap;
    //remove .getRootView() if you want everything but the actionbar.
    View view = getWindow().getDecorView().findViewById(android.R.id.content).getRootView();
    view.setDrawingCacheEnabled(true);
    bitmap = Bitmap.createBitmap(view.getDrawingCache());
    view.setDrawingCacheEnabled(false);

    OutputStream fileOut = null;
    File imageFile = new File(mPath);

    try {
        fileOut = new FileOutputStream(imageFile);
        bitmap.compress(Bitmap.CompressFormat.JPEG, 90, fileOut);
        fileOut.flush();
        fileOut.close();
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    }

    Uri uri = Uri.fromFile(new File(mPath));
    //done. next steps are optional. just displaying screenshot for you in app.
    Picasso.with(this)
           .load(uri)
           .into(mScreenshotImageView);
    mPathTextView.setText("Screenshot saved at: " + mPath);
}
/*
dont forget to include these in your manifest:
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
*/
```
