---
layout: post
title: "Glide 3.0: a media management library for Android"
date: 2014-09-13 00:04
comments: true
categories: Glide
---

![image](https://github.com/bumptech/glide/raw/master/static/glide_logo.png)

Today we are happy to announce the first stable release of [Glide 3.0](https://github.com/bumptech/glide). Glide is an open source media management framework for Android that wraps media decoding, memory and disk caching, and resource pooling into a simple and easy to use interface.

Glide is specifically designed not only to be easy to use, but also to make scrolling lists of images as smooth and pleasant to use as possible. To reduce stuttery scrolling in lists caused by garbage collections due to [Bitmap](http://en.wikipedia.org/wiki/Bitmap) allocations, Glide uses reference counting behind the scenes to track and reuse Bitmap objects. To maximize the number of Bitmaps that are re-used, Glide includes a [Bitmap pool](https://github.com/bumptech/glide/blob/master/library/src/main/java/com/bumptech/glide/load/engine/bitmap_recycle/LruBitmapPool.java) capable of pooling arbitrary sizes of Bitmaps. 

The 3.0 version of Glide includes a number of improvements, including support for animated GIF and video still decoding, improved lifecycle integration to intelligently pause and restart requests, and thumbnailing support. 

Despite all of the new features, Glide’s interface is still simple and easy to use. To display an image, video still, or animated GIF in a view, you still need only one line:

```
Glide.with(context).load("http://goo.gl/h8qOq7").into(yourImageView);
```

Glide will intelligently determine the type of media you’re trying to load, decode it, and return a drawable object that can display the animation or still image in the view. If you want to load specifically a Bitmap, you can do that too:

```
Glide.with(context).load("http://goo.gl/h8qOq7").asBitmap().into(yourImageView);
```

You can also do more complex transformations. For example, to upload the bytes of a 250px by 250px profile photo for a user:

```
Glide.with(context)
        .load("/user/profile/photo/path")
        .asBitmap()
        .toBytes()
        .centerCrop()
        .into(new SimpleTarget<byte[]>(250, 250) {
                 @Override
                 public void onResourceReady(byte[] data, GlideAnimation anim) {
                         // Post your bytes to a background thread and upload them here.
                 }
        });
```

For a more complete list of changes, documentation, or to report issues please see the [Github page](https://github.com/bumptech/glide). To see Glide being used in a real application, check out the recently released source of the [2014 Google I/O app](https://github.com/google/iosched) and their [excellent blog post](https://github.com/google/iosched/blob/master/doc/IMAGES.md) on image loading. Finally, for questions, comments, or suggestions please join our [discussion list](https://groups.google.com/forum/#!forum/glidelibrary).

By Sam Judd, Google Engineering

---


原文地址：[http://google-opensource.blogspot.com/2014/09/glide-30-media-management-library-for.html](http://google-opensource.blogspot.com/2014/09/glide-30-media-management-library-for.html)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)