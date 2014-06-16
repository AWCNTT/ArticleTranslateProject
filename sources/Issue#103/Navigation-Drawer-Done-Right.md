---
layout: post
title: "文章中文标题xxxx"
date: xxxx-xx-xx xx:xx
comments: true
categories: xxxxx(文章关键字，多个关键字用英文空格分割)
---

![navdrawer](http://awcntt-article-image.qiniudn.com/issue103_navdrawer.png)

With [Google+ on Android](https://plus.google.com/+DaveBesbris/posts/Fpsv7ugKX9N) just updated with new navigation menu and ditched navigation drawer, this article might not be applicable anymore in the near future for Android Design, though I don’t think navigation drawer will be phased out very soon.

Finally, [Gmail on Android](https://plus.google.com/u/0/+Gmail/posts/djZoJ7ZaBJ8) has been recently updated with the proper Navigation Drawer interaction pattern (the lower-level edge-swipe drawer access, as well as the Settings/Help/Feedback placement), and I am pretty happy about it because we can finally talk about consistency for this design pattern (I am aware that Google+ and YouTube on Android have yet to change).

I am sure you have (if you always look around for UI/UX articles) come across [this article about how navigation drawer reduces half of the user engagements](http://thenextweb.com/dd/2014/04/08/ux-designers-side-drawer-navigation-costing-half-user-engagement/)  or [why how to avoid hamburger menu](http://lmjabreu.com/post/why-and-how-to-avoid-hamburger-menus/) – if you are not, I would suggest you to read them – these are some interest reads. Although for the Zeebox case, I couldn’t fully understand the decision to go for navigation drawer – it was pretty obvious that navigation drawer is not needed, and I would probably go for the QuickReturnTabs (in the current Twitter app) to regain some of the screen estate – though it’s appreciable that their A/B testing indicates that obvious helps.

These articles (and I think there are more in the wild) suggest that side menu is a bad design pattern and avoid it at all cost, but I say – in Android Design, you can absolutely use it, but only if it’s really necessary and it’s an informed design decision.

##**Understand Navigation Drawer in Android**

In iOS, particularly iOS7, the side menu does clashed with the navigation element (back button) at the top left, as well as the edge-swipe that will act as back (which is not consistently implemented in all Apple apps if I am not mistaken, correct me if I am wrong), however, this is not the case with Android. The navigation drawer for Android is much more [sophisticated](http://developer.android.com/design/patterns/navigation-drawer.html) where the edge-swipe is reserved for accessing the navigation drawer at any lower-level screens (discoverability could a problem here I know), making the top level navigations slightly easier and more accessible even though you are at the deeper level in the app structure. This potentially eliminates the Platform Navigation Pattern Clash mentioned in Luis Abreu’s post (of course he meant in the iOS7).

##**Information Architecture (IA) optimization**

I do agree with [Luis Abreu](http://lmjabreu.com/) about [Information Architecture](http://www.usability.gov/what-and-why/information-architecture.html) optimization when you are tempted to use Navigation Drawer for your app – Navigation Drawer isn’t simply an answer for every navigation need. It is always good to rethink from a high level perspective about the app structure to find out whether the navigation can be made a little bit shallower by removing any unnecessary level/information that doesn’t help exposing the important content to the user – in Android Design Guideline, there is a [pretty nicely written recommendations](http://developer.android.com/design/patterns/app-structure.html) for app structure.

##**Do it right**

![Example](http://awcntt-article-image.qiniudn.com/issue103_example.png)

However, if it’s really necessary to use the navigation drawer as the top-level navigation pattern after careful consideration, just do it, and do it right. It’s not that I do not encourage variations (which is great for a platform), but for UI elements that involved heavily with interactions, it will be always good to stick with the one that is recommended in the design guideline for consistency, familiarity and predictability. We always want the user to ‘learn once, applied everywhere’, especially from an Operating System point of view – I hate to say this but it’s part of the responsibility of the Android developers and designers to help pushing this established* consistency so the user will have less interaction friction when they are shown with a specific UI element. The faster the user able to operate the app and achieve what they want to do = happy user.

*I know some of the Google apps are still yet to be consistent with the latest changes in navigation drawer, but I am sure they are working on it. Mind you that navigation drawer has come a long way and it takes time to get things right ([I wrote about this back in 2012](http://androiduiux.com/2012/06/15/side-navigation-ui-pattern-in-android/))!

##**“What’s out of sight, is out of mind.”**

In [Anthony Rose’s article about Navigation Drawer](http://thenextweb.com/dd/2014/04/08/ux-designers-side-drawer-navigation-costing-half-user-engagement/) and a tweet by Luke Wroblewski below shows that when the navigation pattern is less obvious, the user engagements reduced (although I am not sure what’s the parameters in Luke’s chart).

![image](http://awcntt-article-image.qiniudn.com/issue103_Bk8y16lCYAEGORy.png-large.png)

Sure, for some, these statistics seem worrying – but I don’t think we have yet to see the whole picture in these examples. What does the reduced user engagement means? Does it mean they don’t explore the app anymore or it simply means the first screen (home screen) is already sufficient? Does it mean that the user accomplished what they want to do with the app much faster (with lesser distractions) and thus less engagements? I would probably see this as an achievement if my app is meant to help in productivity because it might means that my app helps the user achieve thing faster.

While I fully agree that we should keep “what’s out of sight, is out of mind” as one of the design principle, but it doesn’t mean that we have to show everything as long as we can, because every UI elements play different role during the user interactions and each of them have it’s own unique importance.

So next time when you come across the use of navigation drawer, make sure it is really necessary to do that – and the new Google+ app just show that Navigation Drawer is not really needed sometimes.

---


原文地址：[http://androiduiux.com/2014/05/20/navigation-drawer-done-right/](http://androiduiux.com/2014/05/20/navigation-drawer-done-right/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)