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
