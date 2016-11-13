---
name: Android libraries
---

# Glide

##### Glide with a recyclerview adapter

```
@Override
public void onBindViewHolder(final ViewHolder holder, final int position) {
    final Model data = mData.get(position);

    Glide.clear(holder.mImageView);

    Glide.with(holder.mImageView.getContext())
         .load(data.user.pic_url)
         .asBitmap()
         .centerCrop()
         .placeholder(R.drawable.profile_pic_placeholder)
         .into(holder.mImageView);
}
```

Must use `Glide.clear()` to make sure image gets set correctly in each row or it will not be reused properly.

With `Glide.with()...`, using `.asBitmap()` is important for when using some libraries such as CircularImageView. Or it will not be set until the recyclerview gets refreshed.

# Retrofit

## File upload

* In API interface file, add your endpoint:

```
public interface FooApi {

    @Multipart
    @PATCH("/users")
    Call<FooResponseVo> uploadPhoto(@Part MultipartBody.Part photo);

}
```

The key parts here are `@Multipart` and `@Part MultipartBody.Part foo`. These tell Retrofit we are uploading a file.

* In your Java code:

```
File picture;
RequestBody requestFile = RequestBody.create(MediaType.parse("multipart/form-data"), picture);
MultipartBody.Part multipartPicture = MultipartBody.Part.createFormData("api_param", picture.getName(), requestFile);

mFooApi.uploadPhoto(multipartPicture);
```

`api_param` in this case is the key that the API is expecting for the file. We are uploading a photo here, so the API is probably looking for `photo` or `file`. The API docs for the API you are working with will say.

*Note: When using @Multipart, ALL other properties of an API call must also be @Part. You cannot use @FormUrlEncoded and @Multipart in the same API call.*

```
@Multipart
@POST("/api/update_profile")
Call<FooResponseVo> fooApiCall(@Part("username") String username, @Part MultipartBody.Part profilePicture);
```

In this example, we have the String username which would usually be `@FormUrlEncoded` and be a `@Field()` or `@Body` (actually, I have not tested body yet. Body might work fine with `@Multipart`) but here, it is a `@Part`.

This takes a String, username, as a part. Some APIs may not be able to process this as the String is sent to API surrounded with double quotes around it. You may need to try this instead (*Note:* when using Strings as part of a Multipart request, there is 1 more thing you must do to get it to work. Continue reading the docs below for "retrofit scalars converter"):

```
File picture;
RequestBody requestFile = RequestBody.create(MediaType.parse("multipart/form-data"), picture);
MultipartBody.Part multipartPicture = MultipartBody.Part.createFormData("api_param", picture.getName(), requestFile);
MultipartBody.Part usernamePart = MultipartBody.Part.createFormData("username", usernameHere);

mFooApi.uploadPhoto(multipartPicture, usernamePart);
```

*Note:* With Strings being a `@Part`, Retrofit takes those strings, serializes it as JSON, and then strings go up to server with an extra set of double quotes around them: `levi` => `"levi"`. In order to combat this, you need to use a special Retrofit plugin.

```
compile 'com.squareup.retrofit2:converter-scalars:2.1.0'
```

Then add to Retrofit initializer:

```
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl(API_URL_BASE)
    .addConverterFactory(ScalarsConverterFactory.create()) // <----- add this.
    .build();
```

with interface defined:

```
@Multipart
@POST("/api/update_profile")
Call<FooResponseVo> fooApiCall(@Part MultipartBody.Part profilePicture, @Part MultipartBody.Part username);
```

## Upload more then one file

Above works for how to upload one single file. For uploading an array of files, things are different:

In this example, say that the API is expecting "an array of picture files with the key 'photos'."

* In the API interface file, add your endpoint:

```
public interface FooApi {

    @PATCH("/users")
    Call<FooResponseVo> uploadManyPhotos(@Body MultipartBody oneOrMorePhotos);

}
```

* In your java code:

```
MultipartBody.Builder builder = new MultipartBody.Builder();
builder.setType(MultipartBody.FORM);

for (File photo: photos) { // where photos is an ArrayList<File> or anything you can iterate over.
    builder.addFormDataPart("photos[]", photo.getName(), RequestBody.create(MediaType.parse("image/jpeg"), photo));
}

MultipartBody requestBody = builder.build();

mFooApi.uploadManyPhotos(requestBody);
```

Retrofit takes care of creating the array for us with `photos[]` as the key. Note that some APIs such as Rails apps will use the `[]` while others may not require it. Up for your testing. I have had success with `[]`.

Found help from [here](http://stackoverflow.com/a/32275127/1486374). [A completely different approach](http://stackoverflow.com/a/34239432/1486374) that may work for other APIs.

## Array of data form type

If the API you are working with requires a form type array: `[foo, bar]`, `[1, 2]`, etc. There is a rule you need to follow:

```
@FormUrlEncoded
@PATCH("/foo/{id}")
Call<FooModel> apiCallWithArray(@Path("id") int id, @Field("array_of_whatever") ArrayList<String> data);
```

Notice that the `data` param is a form field and is an array. While working with Retrofit and the Jackson converter, if you want an integer array then using `int[]` will work for an array but with Strings, `ArrayList<String>` is the only way that will create an array. `String[]` or `List<String>` will not work. ArrayList is the only way to create an array.

Therefore, anytime you need to send an array of data, you might as well use ArrayList.

# PermissionsDispatcher

When asking for permissions at runtime in Android there is lots of boilerplate code. Use [PermissionsDispatcher](https://github.com/hotchemi/PermissionsDispatcher) instead. ([this library also looks promising!](https://github.com/kayvannj/PermissionUtil))

* [Install via Gradle](https://github.com/hotchemi/PermissionsDispatcher#download)
* Setup your fragment or activity with annotations and functions

```
@RuntimePermissions
public class MainActivity extends AppCompatActivity {

    @NeedsPermission(Manifest.permission.CAMERA)
    public void showCamera() {
        getSupportFragmentManager().beginTransaction()
                .replace(R.id.sample_content_fragment, CameraPreviewFragment.newInstance())
                .addToBackStack("camera")
                .commitAllowingStateLoss();
    }

    @OnShowRationale(Manifest.permission.CAMERA)
    public void showRationaleForCamera(PermissionRequest request) {
        new AlertDialog.Builder(this)
            .setMessage(R.string.permission_camera_rationale)
            .setPositiveButton(R.string.ok, new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                request.proceed();
            }
        }).show();
    }

    @OnPermissionDenied(Manifest.permission.CAMERA)
    public void showDeniedForCamera() {
        Toast.makeText(this, R.string.permission_camera_denied, Toast.LENGTH_SHORT).show();
    }

    @OnNeverAskAgain(Manifest.permission.CAMERA)
    public void showNeverAskForCamera() {
        Toast.makeText(this, R.string.permission_camera_neverask, Toast.LENGTH_SHORT).show();
    }
}
```

*Tip: If you need to work with multiple permissions at once such as reading and writing to external storage, you can work with more then one permission:*

```
@OnNeverAskAgain({Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_EXTERNAL_STORAGE})
public void showNeverAskForExternalStoragePermission() {
    Snackbar.make(getView(), R.string.foo, Snackbar.LENGTH_LONG).show();
}
```

`@RuntimePermissions` <-- declare a fragment or activity of requiring permissions
`@NeedsPermission` <--- annotate function that needs permission
`@OnShowRationale` <-- called if you need to explain to the user why you need permission. This is called if the user denies you once and you want to try to get them to accept.
`@OnPermissionDenied` <--- permission denied.
`@OnNeverAskAgain` <-- they marked checkbox saying to never ask again for accepting or denying permission.

The key to these functions is when you ask the user for permissions. Do not ask for permission in the function that is actually writing to external data for example. Ask when a button is pressed. They enter a new fragment. Ask at an appropriate time but not that very second *because* the functions that are annotated cannot return a value they must return void. Therefore, ask in a function that is intended to simply ask the user for permission.

* Build app. This does some processing that needs to happen to continue on. (generates file: `YourActivityOrFragmentNamePermissionsDispatcher`)
* Implement `onRequestPermissionsResult()` function in your activity/function with the annotated functions:

```
@Override
public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults) {
    super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    // NOTE: delegate the permission handling to generated method
    MainActivityPermissionsDispatcher.onRequestPermissionsResult(this, requestCode, grantResults);
}
```

* Lastly, we need to start this whole asking for permission process:

```
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    findViewById(R.id.button_camera).setOnClickListener(v -> {
      // Like mentioned before, ask for permission at a button press or something.
      MainActivityPermissionsDispatcher.showCameraWithCheck(this);
    });
    findViewById(R.id.button_contacts).setOnClickListener(v -> {
      // Like mentioned before, ask for permission at a button press or something.
      MainActivityPermissionsDispatcher.showContactsWithCheck(this);
    });
}
```
