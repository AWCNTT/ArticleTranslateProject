---
layout: post
title: "Android tips and tricks: synchronized scrolling"
date: 2014-08-15 17:10
comments: true
categories: AndroidTips 
---

# Android tips and tricks: synchronized scrolling

Last week we announced the rollout of a completely redesigned Android Market client, and it’s time to dig a little deeper into some of the more interesting UI pieces. As i mentioned in my [Google+ post](https://plus.google.com/108761828584265913206/posts/3t2ngsmUFbR), the team is committed to a level playing field, and the entire UI is done using only published platform APIs. To kick off this series, let’s look at the combined permissions / purchase page:

![image](http://awcntt-article-image.qiniudn.com/issue35_5951013345_6e51bd4495_b.jpg)

Let’s see what happens when the specific application requests a lot of permissions and they don’t fully fit on the screen. As you start scrolling the content, the action bar and the item snippet remain anchored while the rest of the content scrolls:

![image](http://awcntt-article-image.qiniudn.com/issue35_5951568960_83645dd316.jpg) ![image](http://awcntt-article-image.qiniudn.com/issue35_5951013387_4996df9339.jpg)

As the “Accept & buy” button gets to the bottom edge of the item snippet, it snaps to it and does not scroll any more:

![image](http://awcntt-article-image.qiniudn.com/issue35_5951569010_1e254a6b57.jpg) ![image](http://awcntt-article-image.qiniudn.com/issue35_5951568986_ec5ee4b130.jpg)

Let’s take a look at the implementation. The main page is a vertical linear layout with three child views: action bar, item snippet and a scroll view. This scroll view has two children (see below for why this is not strictly correct): the “main” content and a button row:

 ![image](http://awcntt-article-image.qiniudn.com/issue35_5951030167_d01dbf7b8d.jpg) ![image](http://awcntt-article-image.qiniudn.com/issue35_5951030135_7761b25293.jpg)

You can see the bounds of these two views highlighted in yellow in the screenshots above. While the “Accept & buy” button appears to be part of the scrollable content, in fact it is its sibling. The scroll view content has a dummy child view between the pricing content and the tab strip. This view has the same height as the “Accept & buy” button (plus the few dips for the thick green separator) – creating the perfect illusion of unified content.

Keeping the layout of these two siblling views in sync is the real meat. The parts are:

* As [ScrollView](http://developer.android.com/reference/android/widget/ScrollView.html) can have only one child, the two child views highlighted above are placed within one more container. Let’s call it PurchaseLayout.
* The view containing the button and the thick green separator (which snap together to the bottom edge of the item snippet) is placed as the second child of the PurchaseLayout. This ensures that it is painted on top of the main content and is not obscured during the scrolling.
* As ScrollView does not have an API to attach a scroll listener, a custom extension of ScrollView tracks calls to the onScrollChanged method and notifies a custom listener with the new top scroll position.
* As the user scrolls the content, the listener is notified. The listener implementation uses the current top scroll position to determine the vertical position of the “Accept & buy” button. As long as the purchasing part of the page is visible, the buy button is “anchored” to its bottom edge. Once the purchasing part is completely scrolled away, the buy button is “anchored” to the bottom edge of the item snippet. In this case it is effectively the current top scroll position as the buy button is inside the scroll view.

There are two points that deserve a special mention:

* A simpler option would be to have the buy button be a sibling of the scroll view. This has one big problem – as this snapping content (with dark background and thick green separator) is painted on top of the scroll view, it hides the relevant part of the scroll bar. It is usable, but quite ugly.
* When the content is scrolled and we’ve computed the new vertical position for the buy button, we need to re-layout the content of the scroll view to maintain the unified scrolling visuals. This is usually done with a call to View.requestLayout(). This method, however, schedules an asynchronous layout pass. While it is acceptable for one-off layouts, calling it during fast scrolls can cause very visible vertical tear-offs. We saw that multiple consecutive requestLayout() calls were coalesced – and the buy button was lagging behind the fast moving scrolling content. The solution is to use the synchronous [View.offsetTopAndBottom()](http://developer.android.com/reference/android/view/View.html#requestLayout%28%29) to ensure smoothly synchronized scrolling no matter how fast the content is scrolled.

With vertical space being a rare commodity on landscape orientation, this page behaves slightly different. Only the action bar stays anchored, with the item snippet “joining” the rest of the scrollable content. The “Accept & buy” button snaps to the bottom edge of the action bar – which did not require any code changes since the actual vertical position is the current top scroll position.

Stay tuned for more.

---

Related posts:

1. [Android tips and tricks: carousel animations](http://www.pushing-pixels.org/2011/08/15/android-tips-and-tricks-carousel-animations.html) Continuing the series on the more interesting UI pieces of the new Android Market client,...
2. [Android tips and tricks: pixel hunting](http://www.pushing-pixels.org/2011/07/25/android-tips-and-tricks-pixel-hunting.html) Continuing the series on the more interesting UI pieces of the new Android Market client,...
3. [Android tips and tricks: reflections](http://www.pushing-pixels.org/2011/08/01/android-tips-and-tricks-reflections.html) Continuing the series on the more interesting UI pieces of the new Android Market client,...
4. [Android tips and tricks: swipey tabs](http://www.pushing-pixels.org/2011/08/11/android-tips-and-tricks-swipey-tabs.html) Continuing the series on the more interesting UI pieces of the new Android Market client,...


---


原文地址：[http://www.pushing-pixels.org/2011/07/18/android-tips-and-tricks-synchronized-scrolling.html](http://www.pushing-pixels.org/2011/07/18/android-tips-and-tricks-synchronized-scrolling.html)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)