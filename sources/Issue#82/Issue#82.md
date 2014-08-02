---
layout: post
title: "Issue#82"
date: 2014-08-02 22:16

comments: true
categories: Issue#82
---

#Issue #82

###ARTICLES & TUTORIALS
---

[**High Performance / Low Latency Audio on Android **](http://heatvst.com/wp/2013/11/30/high-performance-low-latency-audio-on-android-why-it-still-doesnt-work/)(heatvst.com)

Nils Schneider, the author of the Android music app Heat Synthesizer, has written a blog post about what's wrong with high performance and low latency audio processing on Android. He describes several approaches on how to archive the best results - but some questions are still open.

 
[**Reliable API testing for Android with Retrofit and Mockito **](http://www.mdswanson.com/blog/2013/12/16/reliable-android-http-testing-with-retrofit-and-mockito.html)(mdswanson.com) 

Matt Swanson has published a nice little blog post on how to test tricky things like http requests to an API. He uses Retrofit for the REST calls and Mockito to mock the results.

[**Protip: WebView in a scrolling view (link Email app)**](https://plus.google.com/+PedroLoureiro/posts/FqU9Adzhtrv) (plus.google.com) 

Pedro Loureiro was inspired by some classes he found in the AOSP Mail app.

[**OAT of the ART runtime**](https://plus.google.com/+MatthiasSchaff/posts/WGXSXv2Aukr) (plus.google.com) 

This is the second part of a small series of articles about the newest Android Version: Kitkat. In this part Matthias Schaff will take a closer look at the new fileformat: OAT of the ART runtime and have a brief look at the garbage collection.

[**Mirror API and GDK - Developing for Google Glass #1**](http://www.androidzeitgeist.com/2013/12/mirror-api-gdk-developing-google-glass.html) (www.androidzeitgeist.com) 

Here's a close look at the Google Glass GDK from a developers point of view.

[**Migrating from ActionBarSherlock to ActionBarCompat**](http://www.grokkingandroid.com/migrating-actionbarsherlock-actionbarcompat/) (www.grokkingandroid.com) 

Wolfram Rittmeyer does a thorough job describing how to move from the now end-of-life ActionBarSherlock to Google's ActionBarCompat.

[**Android Out of Memory Error: Causes, Solution and 
Best practices**](http://blogs.innovationm.com/android-out-of-memory-error-causes-solution-and-best-practices/) (blogs.innovationm.com) 

Here are some concrete examples on how to track down those pesky out of memory issues in your app.

[**Restricted Profiles Settings**](http://gmariotti.blogspot.com/2013/12/restricted-profiles-settings.html) (gmariotti.blogspot.com) 

Yesterday Netflix for Android introduced the use of Jelly Bean's Restricted Profiles feature. It is powerful, but very few apps are using it. Gabriele Mariotti shows us how to implement it in our own apps.

[**Smart Watch – Part 2**](http://blog.stylingandroid.com/archives/2206) (blog.stylingandroid.com) 

Mark Allison continues his article series on developing Android apps for smart watches.

###LIBRARIES & CODE
---

[**Dagger 1.2 released**](https://github.com/square/dagger/blob/master/CHANGELOG.md#readme) (github.com) 

Dagger 1.2 is released and available in Maven central. There are numerous performance improvements in both the compiler and runtime e.g. a more effecient string concatenation and read/write locks over global locks.

[**Calligraphy**](https://github.com/chrisjenx/Calligraphy) (github.com) 

Are you fed up of Custom views to set fonts? Or traversing the ViewTree to find TextViews? So was Christopher Jenkins. He recently released this library for simplifying using custom fonts in Android apps.

[**DynamicShareActionProvider**](https://github.com/nikwen/DynamicShareActionProvider) (github.com) 

Here's a custom implementation of the ShareActionProvider by Google -- Sharing is done dynamically now! This means that your app specifies the type of the shared data first and generates the data when an app is chosen for sharing. No more need to define what to share when the Activity is created.

[**AndroidStaggeredGrid**](https://github.com/etsy/AndroidStaggeredGrid) (github.com) 

An Android staggered grid view which supports multiple columns with rows of varying sizes.

###TOOLS
---
 
[**android-holo-colors-idea-plugin**](https://github.com/jeromevdl/android-holo-colors-idea-plugin) (github.com) 

A plugin for Android Studio that is like android-holo-colors.com: you choose a name for your theme, you select the minSDKVersion, the compat library if necessary, and the widgets you need and it generates colored resources for you.

###DESIGN
---

[**Android Mockup & Design Tool**](https://plus.google.com/+NikolaiPrettner/posts/BAY3ZigL2VV) (plus.google.com)

Nikolai Prettner has shared a mockup tool for concepts and mockup designs. It includes tons of different styles from various different Google Apps as well as the new version of Android - 4.4 Kitkat.

---


原文地址：[http://androidweekly.net/issues/issue-82](http://androidweekly.net/issues/issue-82)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)