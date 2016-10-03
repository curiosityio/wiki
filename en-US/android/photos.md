---
name: Photos
---

# Scale down images on Android by maximum file size

```
private Bitmap scaleDownImage(Context context, Uri image) {
    final int IMAGE_MAX_SIZE = 1200000; // 1.2MP

    try {
        InputStream inputStream = context.getContentResolver().openInputStream(image);

        // Decode image size
        BitmapFactory.Options bitmapOptions = new BitmapFactory.Options();
        bitmapOptions.inJustDecodeBounds = true;
        BitmapFactory.decodeStream(inputStream, null, bitmapOptions);
        inputStream.close();

        int scale = 1;
        while ((bitmapOptions.outWidth * bitmapOptions.outHeight) * (1 / Math.pow(scale, 2)) > IMAGE_MAX_SIZE) {
            scale++;
        }

        Bitmap bitmap;
        inputStream = context.getContentResolver().openInputStream(image);
        if (scale > 1) {
            scale--;
            // scale to max possible inSampleSize that still yields an image
            // larger than target
            bitmapOptions = new BitmapFactory.Options();
            bitmapOptions.inSampleSize = scale;
            bitmap = BitmapFactory.decodeStream(inputStream, null, bitmapOptions);

            // resize to desired dimensions
            int height = bitmap.getHeight();
            int width = bitmap.getWidth();

            double y = Math.sqrt(IMAGE_MAX_SIZE / (((double) width) / height));
            double x = (y / height) * width;

            Bitmap scaledBitmap = Bitmap.createScaledBitmap(bitmap, (int) x, (int) y, true);
            bitmap.recycle();
            bitmap = scaledBitmap;

            System.gc();
        } else {
            bitmap = BitmapFactory.decodeStream(inputStream);
        }
        inputStream.close();

        return bitmap;
    } catch (IOException e) {
        return null;
    }
}
```

# Scale down images by maximum height/width

```
private Bitmap resize(File image) {
    String imagePath = Uri.fromFile(image).getPath();

    int maxWidth = 1690;
    int maxHeight = 896;

    BitmapFactory.Options opts = new BitmapFactory.Options();

    opts.inJustDecodeBounds = true;
    Bitmap bitmap = BitmapFactory.decodeFile(imagePath, opts);

    int originalBitmapHeight = opts.outHeight;
    int originalBitmapWidth = opts.outWidth;

    int resizeScale = 1;
    //get the good scale
    if (originalBitmapWidth > maxWidth || originalBitmapHeight > maxHeight) {
        final int heightRatio = Math.round((float) originalBitmapHeight / (float) maxHeight);
        final int widthRatio = Math.round((float) originalBitmapWidth / (float) maxWidth);
        resizeScale = heightRatio < widthRatio ? heightRatio : widthRatio;
    }
    //put the scale instruction (1 -> scale to (1/1); 8-> scale to 1/8)
    opts.inSampleSize = resizeScale;
    opts.inJustDecodeBounds = false;

    int futureBitmapSize = (originalBitmapWidth / resizeScale) * (originalBitmapHeight / resizeScale) * 4;
    //check if it's possible to store into the vm java the picture
    if (Runtime.getRuntime().freeMemory() > futureBitmapSize) {
        bitmap = BitmapFactory.decodeFile(imagePath, opts);
    } else {
        return null;
    }

    return bitmap;
}
```
