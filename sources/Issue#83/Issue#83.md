---
layout: post
title: "Issue#83"
date: 2014-08-02 22:16

comments: true
categories: Issue#83
---

#Issue #83

###ARTICLES & TUTORIALS
---

[**Android Recipe #4, path tracing**](http://www.curious-creature.org/2013/12/21/android-recipe-4-path-tracing/) (www.curious-creature.org)

Romain Guy may have left the Android team, but there are still many visual effects and demos he's ready to share with developers. In this article, he writes about path tracing effects (and a couple other bonus visual effects).

[**A journey on the Android Main Thread**](http://corner.squareup.com/2013/12/android-main-thread-2.html) (corner.squareup.com) 

A nice post by the square dev team about lifecycle of an Activity. You'll learn about how orientation changes affects the main thread and how to resolve lifecycle issues.

[**Smart Watch – Part 3**](http://blog.stylingandroid.com/archives/2214) (blog.stylingandroid.com)

Third post by Mark Allison on how to develop for the Android Smart Watch.

 
[**TextClock Version 2 – Part 1 **](http://blog.stylingandroid.com/archives/2242)(blog.stylingandroid.com) 

Mark Allison revisits his text clock project from January 2013 to add some KitKat touches.

[**Using the new Gradle-based Android build system**](https://plus.google.com/+CyrilMottier/posts/WRgB2shaeuu) (plus.google.com) 

Cyril Mottier shares a newer example of how to set up a professional project using the new Gradle build system.

[**A Recipe for writing responsive REST clients on Android**](http://birbit.com/a-recipe-for-writing-responsive-rest-clients-on-android/) (birbit.com) 

Android development is easy but making the app resilient is hard. Yigit Boyar shares some ideas and some code to make it easier.

[**ProTip: Troubleshooting app bugs related to scheduled tasks**](https://plus.google.com/+RomanNurik/posts/5w3Mi9EoniT) (plus.google.com) 

Roman Nurik shares a tip about two awesome tools for troubleshooting bugs related to scheduled tasks.

[**RxJava and Android: working with subjects **](http://howrobotswork.wordpress.com/2013/12/23/rxjava-and-android-working-with-subjects/)(howrobotswork.wordpress.com) 

RxJava’s observers and observables offer a great way to manage multiple threads and async processes in Android development.

###LIBRARIES & CODE
---

[**AndroidStaggeredGrid**](https://github.com/etsy/AndroidStaggeredGrid) (github.com) 

An Android staggered grid view which supports multiple columns with rows of varying sizes.

[**Android Demo for beginners**](https://github.com/nicolasjafelle/AndroidDemo) (github.com) 

This nice demo code by Nicolas Jafelle shows you how to create a Fragment and associate to an Activity, make asynchronous call using SafeAsyncTask, connect to Foursquare's API using Retrofit with OkHttp and parse Json with Gson.

[**Sprinkles**](https://github.com/emilsjolander/sprinkles) (github.com) 

Sprinkles is a boiler-plate-reduction-library for dealing with databases in android applications. It uses annotations to enrich your model classes and it has a lot of cool features like migrations, transactions and out of the box cursors. It does not support relations and this is by design. The code is on github and soon on maven central.

[**GhostLog**](https://github.com/jgilfelt/GhostLog) (github.com) 

Jeff Gilfelt has released Ghost Log as open source. It's an Android application that displays the device logcat buffer in a system overlay window.

###NEWS
---

[**SecretKeyFactory API in Android 4.4**](http://android-developers.blogspot.co.uk/2013/12/changes-to-secretkeyfactory-api-in.html) (android-developers.blogspot.co.uk)

In Android 4.4 KitKat Google made a subtle change to the behavior of SecretKeyFactory. This change may break some applications that use symmetric encryption and meet all of the following conditions: Use SecretKeyFactory to generate symmetric keys, and use PBKDF2WithHmacSHA1 as their key generation algorithm for SecretKeyFactory, and Allow Unicode input for passphrases.

 
[**1-Star Ratings Since 'Quick Rating' Widget**](http://www.androidpolice.com/2013/12/30/multiple-developers-seeing-influx-of-unexplained-1-star-ratings-since-launch-of-play-stores-new-quick-rating-widget/) (www.androidpolice.com) 

Earlier this month an update to the Play Store began rolling out with a brand new ratings widget appearing on the main landing page and within landing pages for each major section. Several developers are reporting a drastic rise in 1-star ratings on otherwise successful apps.

[**Programming Mobile Applications for Android Handheld Systems**](https://www.coursera.org/course/android?from_restricted_preview=1&course_id=971246) (www.coursera.org) 

A free Coursera course starting on January 21st: Introduction to the design and implementation of applications for handheld systems, such as smartphones and tablets, running the Android Platform.

###BUSINESS
---

[**Do Not Track California Privacy Law Changes Effective Today**](https://www.iubenda.com/blog/2014/01/01/track-california-privacy-law-changes-effective-today/) (www.iubenda.com) 

If you publish apps in the United States, it is important to know about California's Do Not Track privacy law, CalOPPA and changes that went into effect Jan 1, 2014.

---


原文地址：[http://androidweekly.net/issues/issue-83](http://androidweekly.net/issues/issue-83)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)