---
layout: post
title: "Creating Custom Layouts for Android"
date: 2014-08-09 13:25
comments: true
categories: Custom Layouts
---

#Creating Custom Layouts for Android

There are a lot of great articles on Android development on the web but one field that doesn’t feel explored enough is creating custom Layouts. For one thing, the Android framework does spoil you with a bunch of layouts that usually fit most of the usually designed interfaces. The problem comes when faced with slightly unconventional UIs. A basic knowledge of how layouts work may help you avoid creating a mess of nested layouts when a quick custom one may have sufficed. The design below for example was accomplished with one custom layout object.

<img height="300px" src="http://awcntt-article-image.qiniudn.com/issue31_Screenshot_2012-06-28-00-27-34.png" />

I have to admit I am not a fan of some fundamental choices made on the UI architecture side. For one thing Layouts are [Views](http://developer.android.com/reference/android/view/View.html) (or more specifically [ViewGroups](http://developer.android.com/reference/android/view/ViewGroup.html)) themselves and not only position and size the elements they are supposed to but also add them as children to themselves. This means that if you wanted to create an experience where you maybe started with a grid of photos and when the user clicks on one of them, lay the photos out in a row, you can’t really do that well since the photos have to be unparented from one View (the GridLayout) and then added as children to another. And don’t hope for any animations in between. In the coming months I hope to create an open source project for some custom layouts that separated a views parent from its layout (Adobe Flex went the same route between Flex 3 and Flex 4. Flex 3 had HBoxes, VBoxes etc but were deprecated in Flex 4 with [Spark Layouts](http://help.adobe.com/en_US/flex/using/WS0141D24B-6AEB-4721-BA04-9BF15F86350F.html) that could be attached dynamically. If you are a Flex software engineer, the Android architecture will look very familiar).

But this post explains the layout architecture as is. So lets begin. The attached code blocks are from a custom Layout example I wrote and is available on Github. Its a very simplistic LinearLayout that sizes its children equally. You can grab the github project [here](https://github.com/arpit/CustomLayoutExample).

The base class for a Layout is a ViewGroup that basically extends a View and has hooks for things like [addView](http://developer.android.com/reference/android/view/ViewGroup.html#addView(android.view.View,%20int,%20android.view.ViewGroup.LayoutParams)) etc. To create a custom ViewGroup, the only method you need to override is [onLayout](http://developer.android.com/reference/android/view/ViewGroup.html#onLayout(boolean,%20int,%20int,%20int,%20int)). The onLayout is triggered after the ViewGroup itself has finished laying itself out inside its own container ViewGroup and is now responsible for laying out its children. It should call the [layout](http://developer.android.com/reference/android/view/View.html#layout(int,%20int,%20int,%20int)) method on all of its children to now position and size them (the left and top parameters will determine the child view’s x and y and the right and bottom will determine its width (right – left) and height (top-bottom).

```
@Override
protected void onLayout(boolean changed, int l, int t, int r, int b) {
     int itemWidth = (r-l)/getChildCount();
     for(int i=0; i< this.getChildCount(); i++){
         View v = getChildAt(i);
	 v.layout(itemWidth*i, t, (i+1)*itemWidth, b);
         v.layout(itemWidth*i, 0, (i+1)*itemWidth, b-t); (Fixed bug mentioned by Nathaniel Wolf in the comments below)
     }
}
```

One thing to note is that at this point you are playing at the pixel level and not with the pixel abstraction units like dips. But besides that you should be fine.

The problem here is that while this will layout any simple views (i.e. Views that aren’t layouts themselves), any child layout objects aren’t visible at all. This is because the child layouts have no idea till that time how they should lay out their own children since they haven’t been measured at all and return a measured width and height of 0. To do a layout correctly, you need to make sure that you also override the onMeasure method in your layout and call the measure method on each of your children appropriately.

During measure, you need to first calculate your own measuredWidth and measuredHeight and then based on that tell your children how they need to size themselves. For example a horizontal LinearLayout might say “My measuredWidth is 100 plxels and I have two children so each must measure exactly 50 pixels. This it would do my passing a MeasureSpec object which defines how the child should interpret the measurement its receiving: either EXACTLY, AT MOST or UNSPECIFIED. The child view then uses those cues to create its own measuredWidth and measuredHeight (by usually calling setMeasuredDimension at some point in the onMeasure).

```
@Override
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec){

     //At this time we need to call setMeasuredDimensions(). Lets just 
     //call the parent View's method 
     //(see https://github.com/android/platform_frameworks_base/blob/master/core/java/android/view/View.java) 
     //that does:
     //setMeasuredDimension(getDefaultSize(
     //                       getSuggestedMinimumWidth(), widthMeasureSpec),
     //                    getDefaultSize(
     //                       getSuggestedMinimumHeight(), heightMeasureSpec));
     //		

      super.onMeasure(widthMeasureSpec, heightMeasureSpec);
      int wspec = MeasureSpec.makeMeasureSpec(
                  getMeasuredWidth()/getChildCount(), MeasureSpec.EXACTLY);
      int hspec = MeasureSpec.makeMeasureSpec(
                  getMeasuredHeight(), MeasureSpec.EXACTLY);
      for(int i=0; i<getChildCount(); i++){
         View v = getChildAt(i);
         v.measure(wspec, hspec);
      }
}
```

Note that the measuredWidth and measuredHeight are cues for the parent layout when its laying out the children. It might still decide to ignore the measured width and height and lay them out as it feels since its up to it to define the left, right, top, bottom values during onLayout but a good citizen of the layout world will probably not ignore them.

Also I am just getting into this so I may have missed something so please drop me a comment or let me know on [Google+](https://plus.google.com/115089607132986274709/posts) and we can have a discussion.

[Update 1] I have written an extension to this post that includes [adding custom attibutes and layoutparams](http://www.arpitonline.com/blog/2012/08/06/android-custom-layouts-custom-attributes-and-layoutparams/) to your layout class

[Update 2] Great talk on this topic from Google I/O 2013

<div>
<iframe style="line-height: 1.5em;" src="http://www.youtube.com/embed/NYtB6mlu7vA" allowfullscreen="" frameborder="0" id="fitvid999988">
</iframe>
</div>


---


原文地址：[http://arpitonline.com/blog/2012/07/01/creating-custom-layouts-for-android/?utm_source=androidweekly](http://arpitonline.com/blog/2012/07/01/creating-custom-layouts-for-android/?utm_source=androidweekly)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)