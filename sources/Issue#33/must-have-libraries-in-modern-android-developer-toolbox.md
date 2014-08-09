---
layout: post
title: "Must-have Libraries in Modern Android Developer Toolbox"
date: 2014-08-09 16:10
comments: true
categories: Libraries
---

#Must-have Libraries in Modern Android Developer Toolbox

Android is a platform that for better or for worse runs on wide variety of devices and causes a lot of “fragmentation” complaints by less experienced developers. And while the screen/resolution issues can be decently solved by having a competent designer (see: web development), version fragmentation poses a more significant problem - only 7.1% of all Android devices run 4.x versions, while the rest are suck on older Androids with little hope for update.

![Android version distribution](https://www.virag.si/images/posts/2012_06_02_chart.png)

Ice Cream sandwich (and Honeycomb before it) brought significant improvements to the Android API, which significantly ease cross-device development and thanks to efforts of several developers a large part of those changes were backported in form of libraries for Android 2.x.
The most headache-easing libraries for Android development I’ve found so far are:

####[1. Android Support Package](http://developer.android.com/sdk/compatibility-library.html)

This is official Google library which backports [Fragments](http://developer.android.com/guide/topics/fundamentals/fragments.html) an [Loaders](http://developer.android.com/guide/topics/fundamentals/loaders.html).

* [**Fragments**](http://developer.android.com/guide/topics/fundamentals/fragments.html) add a way to manage only part of displayed activity life-cycle and are critical part of tablet user interfaces - especially if you want to develop apps which work on phones and tablets without publishing separate APKs.

* [**Loaders**](http://developer.android.com/guide/topics/fundamentals/loaders.html) are a high-level interface for retrieving data from slow sources (network or database). Android runtime caches the loaders by their ID across Activity life-cycle, which means easy management and caching of remote data without writing boiler-plate AsyncTask code to keep state across orientation changes.

####[2. Action Bar Sherlock](http://actionbarsherlock.com/)

ABS is a library by [Jake Wharton](http://jakewharton.com/) that backports the [Action bar API](http://developer.android.com/guide/topics/ui/actionbar.html) to Android 2.x.

Action bar is a new Android paradigm, which is composed of a top bar with application name and implements tab navigation, menu replacement and “up” navigation. Pretty much any Android app has an action bar (or at least should have) and this API is the easiest way to implement it. It also handles tab navigation with optional split mode (you should not use TabHost anymore) and menu buttons on devices without physical keys (e.g. Galaxy Nexus) and moves icons to menu when screen space runs out.

!['Boid Twitter client with split action bar'](https://www.virag.si/images/posts/2012_06_02_boid.png)

More details of how to use action bar (and why it’s important) are available on [Android Design page](http://developer.android.com/design/patterns/actionbar.html).

Seriously, if you’re not using it yet, you should.

####[3. Action Bar Style Generator](http://jgilfelt.github.com/android-actionbarstylegenerator/)

[Action Bar Style Generator](http://jgilfelt.github.com/android-actionbarstylegenerator/) is a nice little web tool by [Jeff Gilfelt](https://twitter.com/#!/readyState), which generates styles and 9-patch images in selected colors. It’s compatible with before-mentioned ActionBarSherlock library and is a must-have if you want custom branded application.

####[4. Nine Old Androids](http://nineoldandroids.com/)

[Nine Old Androids](http://nineoldandroids.com/) is another gem from [Jake Wharton](http://jakewharton.com/), which ports 3.x+ animation API to Android 1.x+.

Android animation pre-3.x was very clunky and required XML files to do anything useful. Honeycomb made everything easier (but not perfect) by introducing [view property animators](http://android-developers.blogspot.com/2011/05/introducing-viewpropertyanimator.html). This is thus a must-have for an app that wants to be compatible with 2.x and visually appealing at the same time.

#### [5. Pull-to-Refresh](https://github.com/chrisbanes/Android-PullToRefresh)

[Pull-to-Refresh](https://github.com/chrisbanes/Android-PullToRefresh) allows you to implement the iOS-ish paradigm of pulling a ListView to refresh content.

!['Pull-to-refresh example'](https://www.virag.si/images/posts/2012_06_02_ptr.png)

**NOTE**: Pull-to-refresh is a distinctly non-Android way of refreshing content, I suggest adding a separate “Refresh” option to the menu (e.g. like Boid Twitter client) to avoid user confusion.

#### [6. RoboGuice](http://code.google.com/p/roboguice/)

[RoboGuice](http://code.google.com/p/roboguice/) is a dependency-injection framework for Android, which works perfectly for Android UI elements and services. It helps eliminate all those pesky findViewById(), getIntent().getExtras() calls littering the Activity code, making it more readable and (because of some added checks) less error-prone.
There were some problems using both RoboGuice and ABS in the same project (since both extend the Activity class), however this issue has been fixed in latest version of ABS [see example](https://github.com/JakeWharton/ActionBarSherlock/tree/master/samples/roboguice) on ABS GitHub.

###[7. ViewPagerIndicator](http://viewpagerindicator.com/)

Android Support Library also adds support for horizontally scrolling [ViewPager](http://developer.android.com/reference/android/support/v4/view/ViewPager.html), which allows you to put several fragments side by side and allow user to swipe between them. However, the API is missing a position indicator similar to the one in Google+ and Play Market apps. Such customizable indicator is provided by [ViewPagerIndicator](http://viewpagerindicator.com/) library.

!['View pager indicator tabs'](https://www.virag.si/images/posts/2012_06_02_tabs.png)

###[8. AQuery](http://code.google.com/p/android-query)

Noone in their right mind would say Java is a concise language - it often needs alot of boilerplate code to do things. [AQuery](http://code.google.com/p/android-query) is a library that tries to fix this problem by adding a more fluent API over Android platform API, cleaning up your codebase.

Thanks to [Damjan Malis](https://twitter.com/#!/malisd) for last two suggestions.
That’s it for now, if there are any other great Android libraries I’d be happy to know.


---


原文地址：[https://www.virag.si/2012/06/must-have-libraries-in-modern-android-developer-toolbox/](https://www.virag.si/2012/06/must-have-libraries-in-modern-android-developer-toolbox/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)