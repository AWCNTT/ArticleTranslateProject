---
layout: post
title: "Android Development – Using Custom Fonts"
date: 2014-08-15 17:29
comments: true
categories: Android Fonts
---

# Android Development – Using Custom Fonts

An Android device comes with 3 basic fonts – Droid Sans, Droid Sans Mono, and Droid Serif. These are sufficient for every day use, but for your custom apps you will most probably want to use your own font. This post will show you how you can do so.

### First Steps

First step is to pick a font that you want to use. For this example, we’ll be using BPReplay, an OpenType font that I stumbled across here.

![bpReplay](http://awcntt-article-image.qiniudn.com/issue35_bpReplay_thumb.png)

Hot looking font! In order to access our font easily, we need to bundle it with our application in a way that our code can subsequently load it. To do this, we create a Fonts folder in our assets directory and copy our font there.

![fontsFolder](http://awcntt-article-image.qiniudn.com/issue35_fontsFolder_thumb.png)

That’s it for the setup, now on to the code.

### The Code

To access our custom font, we use the Typeface class in the Android SDK to create a typeface that Android can use, then set any display elements that need to use our custom font appropriately. To demonstrate, we’ll create two text views on our main screen, one using the default Android Sans font, and the other using our BPReplay font. The layout is below.

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <TextView
        android:id="@+id/DefaultFontText"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:textSize="30sp"
        android:text="Here is some text." />
    <TextView
        android:id="@+id/CustomFontText"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:textSize="30sp"
        android:text="Here is some text.">
        </TextView>
</LinearLayout>
```
The code to load and set the custom font is straight forward as well, and is shown below.

```
public class Main extends Activity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
 
        Typeface tf = Typeface.createFromAsset(getAssets(),
                "fonts/BPreplay.otf");
        TextView tv = (TextView) findViewById(R.id.CustomFontText);
        tv.setTypeface(tf);
    }
}
```

You can see on line 8 where we instantiate a new Typeface object from our font definition file. On lines 9 and 10, we set the text view to use our custom font. The result us shown below.

![customFont1](http://awcntt-article-image.qiniudn.com/issue35_customFont1_thumb.png)

Sure enough, we have a different font on the screen. Using another freeware font called Molot, our font goodness becomes even more apparent.

![customFont2](http://awcntt-article-image.qiniudn.com/issue35_customFont2_thumb.png)


---


原文地址：[http://www.barebonescoder.com/2010/05/android-development-using-custom-fonts/](http://www.barebonescoder.com/2010/05/android-development-using-custom-fonts/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)