---
layout: post
title: "Don't Advertise Intent Filters That Are Not Yours"
date: 2014-08-09 14:10
comments: true
categories: Intent Filters
---

#Don't Advertise Intent Filters That Are Not Yours

About 12 hours ago, somebody [posted a question to StackOverflow](http://stackoverflow.com/questions/11388450/using-zxing-barcode-scanner-causes-securityexception/11394193) that hits upon another Android anti-pattern: copying and pasting `<intent-filters>` without thinking.

The [UPS Mobile](https://play.google.com/store/apps/details?id=com.ups.mobile.android) app allows you to track packages and do a handful of other things that you might ordinarily do via the UPS Web site. It generally seems to be well-regarded, but it has an annoying flaw:

It claims to be Barcode Scanner, and does a lousy job at it.

[Barcode Scanner](https://play.google.com/store/apps/details?id=com.google.zxing.client.android), from [ZXing](http://code.google.com/p/zxing/), is a favorite among Android developers for its integration possibilities. However, some people do not like having a dependence upon the Barcode Scanner app, so they grab the open source code and attempt to blend it into their own apps. This is neither endorsed nor supported by the ZXing team, but since it is open source, it is also perfectly legitimate.

However, UPS (or whoever they hired to build the app) screwed up. They not only copied the source code, but they copied the manifest entry for the scanning activity. And, their activity has:

```
<intent-filter>
  <action android:name="com.google.zxing.client.android.SCAN" />
  <category android:name="android.intent.category.DEFAULT" />
<intent-filter>
```

This means that on any device that has UPS Mobile installed, they will be an option for handling Barcode Scanner Intents. What happened was that the person asking the question was manually invoking startActivityForResult() to bring up Barcode Scanner, was getting a chooser with UPS Mobile in it, and then was crashing upon choosing UPS Mobile… because UPS Mobile declared this activity to be not exported.

Due to [this bug](http://code.google.com/p/android/issues/detail?id=29535), Android will display non-exported activities in a chooser, despite the fact that they can never be successfully used by the user.

So, what should we learn from this?

First, UPS Mobile should not have used that <intent-filter>. As Dianne Hackborn has pointed out, your <intent-filter> mix [is effectively part of your app’s API](http://android-developers.blogspot.com/2011/06/things-that-cannot-change.html), and so you need to think long and hard about every <intent-filter> you publish. UPS Mobile is not Barcode Scanner and should not be advertising that they handle such Intents, despite the activity being not exported.

Second, UPS Mobile probably should not have had any <intent-filter> elements for this activity, if they intend to use it purely internally. They could just as easily use an explicit Intent to identify the activity and avoid all of this nonsense.

Third, the person who filed the SO question ideally would have been using ZXing’s IntentIntegrator. As Sean Owen of the ZXing project noted in a comment on my answer, IntentIntegrator ensures that only Barcode Scanner or official brethren will handle any scan requests, so this problem would not have appeared.

Fourth, Android really should not be showing non-exported activities in a chooser, which means probably that PackageManager should be filtering out non-exported activities from methods like `queryIntentActivities()`, which I presume lies at the heart of the chooser.


---


原文地址：[http://commonsware.com/blog/2012/07/09/dont-advertise-intent-filters-that-are-not-yours.html](http://commonsware.com/blog/2012/07/09/dont-advertise-intent-filters-that-are-not-yours.html)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)