---
layout: post
title: "Issue#86"
date: 2014-08-02 22:16

comments: true
categories: Issue#86
---

#Issue #86

###ARTICLES & TUTORIALS
---

[**Android String Formatting with Phrase **](http://corner.squareup.com/2014/01/phrase.html)(corner.squareup.com)

Phrase is a micro-library for text token replacement on Android. Phrase solves a few simple problems Square noticed after translating Square Register to French and Japanese.

 
[**Drawing shapes with fingers **](http://gmariotti.blogspot.com/2014/01/drawing-shapes-with-fingers.html)(gmariotti.blogspot.com) 

Gabriele Mariotti show how to implement drawing on a canvas based on touch events.

[**Thread Scheduling in Android**](http://www.androiddesignpatterns.com/2014/01/thread-scheduling-in-android.html) (www.androiddesignpatterns.com) 

Alex Lockwood gives an overview of how thread scheduling works in Android, and will briefly demonstrate how to explicitly set thread priorities yourself to ensure that your application remains responsive even as multiple threads run in the background.

[**SurfaceView Video Cropping**](https://github.com/dmytrodanylyk/dmytrodanylyk/blob/gh-pages/articles/surface-view-video-cropping.md) (github.com) 

In this tutorial you'll create an application which will do following: display video from assets folder using TextureView, and when user click on screen, TextureView should be resized and video should be cropped to match new view size

[**Android Staggered Grid**](http://codeascraft.com/2014/01/13/android-staggered-grid/) (codeascraft.com)

While building the new Etsy for Android app, a key goal for our team was delivering a great user experience regardless of device. This article dives into the details of implementing their newly open sourced grid control.

 
[**Simpler Android apps with Flow and Mortar**](http://corner.squareup.com/2014/01/mortar-and-flow.html) (corner.squareup.com) 

An interesting blog post where the square dev team suggests an alternative to Fragments. They use an URL style router "Flow" to structure your app into different screens. "Mortar" is leveraged to build all the precious object when you trigger a route. Each section of a Mortar app (each screen if you’re also using Flow) is defined by a Blueprint with its very own module. This module is most commonly provided as a singleton Presenter, a view controller with a simple lifecycle and its own persistence bundle. Check out the libraries in the libraries and code section.

[**TextClock Version 2 – Part 4**](http://blog.stylingandroid.com/archives/2277) (blog.stylingandroid.com) 

Fourth part of creating a text clock by Mark Allison.

###LIBRARIES & CODE
---

[**Effortless Android Networking**](http://sahan.me/RoboZombie) (sahan.me) 

1.RoboZombie accepts an annotated interface which describes a remote service to give you a proxy implementation of it. 
2.Invoke the methods on the proxy to make network calls.

[**Discrollview**](https://github.com/flavienlaurent/discrollview) (github.com) 

Websites using a pattern called the "discrollver" pattern - when you scroll, widgets appear from nowhere by fade, translation or scale. With DiscrollView, Flavien Laurent wants to bring this pattern on Android.

[**CircleImageView**](https://github.com/hdodenhof/CircleImageView) (github.com) 

A fast circular ImageView perfect for profile images. It can be used with all kinds of drawables, i.e. a PicassoDrawable from Picasso or other non-standard drawables.

[**FadingActionBar**](https://github.com/ManuelPeinado/FadingActionBar) (github.com) 

FadingActionBar is a library which implements the cool fading action bar effect that can be seen in the Play Music app. It has just been updated to v3.1.0. The biggest change is that you can put your content inside a WebView. Check the new sample that showcases this, it's quite neat.

[**Flow**](https://github.com/square/flow/) (github.com) 

Flow is a small library that helps with describing an app as a collection of moderately independent screens. These screens can be pushed onto a concrete backstack to provide navigation history.

[**Mortar**](https://github.com/square/mortar/) (github.com) 

Mortar is a simple library that makes it easy to pair thin views with dedicated controllers (Presenters), isolated from most of the vagaries of the Activity life cycle.

[**Michelangelo**](https://github.com/RomainPiel/Michelangelo) (github.com) 

Layout inflation library for Android which uses annotation processing to write the code you don't want to write and simplify your compound views.

###NEWS
---

[**Help out DevAppsDirect**](https://plus.google.com/110117120873163164212/posts/5kmEHSqA9pR) (plus.google.com)
DevAppsDirect is a useful Android app that has a large and growing list of demo apps included. The author is now appealing to the general developer community to help with development and integration of libraries.

 
[**Google Works on a Fitness API for Android**](http://googlesystem.blogspot.com/2014/01/google-works-on-fitness-api-for-android.html) (googlesystem.blogspot.com) 
There's some evidence that Google will be releasing APIs for sharing or storing fitness data.

###TOOLS
---

[**ADB Plugin for Android Studio**](https://plus.google.com/+PhilippeBreault/posts/Hr5E8kHmvBB) (plus.google.com) 

Philippe Breault has created a handy plugin for Android Studio which can help automate your manually uninstalling, restarting or clearing the data of your app.

---


原文地址：[http://androidweekly.net/issues/issue-86](http://androidweekly.net/issues/issue-86)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)