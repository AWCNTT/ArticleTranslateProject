---
layout: post
title: "Kindly Asking for Ratings and Reviews"
date: 2014-09-12 22:56
comments: true
categories: Animation
---

![image](http://ryanharter.com/images/posts/kindly-asking-for-ratings/fragment_ratings.png)

One of the most important factors in determining whether or not people will buy your app is the rating. Especially if your app isn’t free, people will generally check the ratings and reviews before downloading your app. And if your app is a paid app, ratings and reviews can mean success or failure for your app.

When launching [Fragment](http://fragmentapp.com/) for Android, our first though was making a kick ass app, but then we needed to think about how to ensure that users remembered to go back to the Play Store to share their experience by rating the app. It’s quite common for apps to display a generic dialog after a while prompting the user to rate the app, but we thought we could make this experience better.

There was a lot of discussion in the Pixite team about how to encourage people to rate the app without being annoying. Here are some of the things we considered and learned.

### How to Ask for a Rating

Users are used to being asked for a rating after using an app for a little while, but many apps make this annoying by displaying a dialog at some random point, usually when you launch the app. We find this annoying when we use apps, so we didn’t want to bother our users with something so brash.

We decided that the best way to ask for a rating would be to simply put a button in Fragment UI. This allowed us to ask for a rating without interrupting or surprising the user, and gives us a lot of control over where and how this is presented. Here’s an example of what the user sees:

![image](http://ryanharter.com/images/posts/kindly-asking-for-ratings/rating.png)

As you can see, this rate button isn’t invasive and the user can easily ignore it if she chooses, but it’s still there. We think that this is a great way to ask the user for a rating without being annoying.

### When to Ask for a Rating

Another important question is when to ask the user for a rating. We wanted to make sure that we didn’t bother our users as they were still getting their feet wet, as they wouldn’t have had enough time to get to know the app and make an informed review.

With Fragment, we decided to wait to even show the rating button until the user had launched the app 3 times. This seemed like long enough that a user would have a good idea of the capabilities of the app.

Another important thing to note about the Fragment rate button is that it appears on the Export screen. This means that a user will never see the button until they get something they want to save or share. At this point, the user has really used the app, making works of art that they are proud of, and will hopefully have enjoyed the app enough to provide a good rating.

The other thing to note is that, once a user has rated the app, we don’t want to keep bothering them. Therefore, once the user taps the Rate button, it goes away, never to return.

### Finishing Touches

The last important piece about the rating button is it’s animation. My concern with having a button appear after having used the app several times is that users might not notice it. Therefore, I used the [helpful techniques](https://plus.google.com/+CyrilMottier/posts/FABaJhRMCuy) of [Cyril Mottier](http://cyrilmottier.com/) to add a subtle animation to the button to grab the user’s attention.

![image](http://ryanharter.com/images/posts/kindly-asking-for-ratings/rating-animation.gif)

An animation like that every time the user exported a photo would be terribly annoying, though, so this only happens the first time the button is made visible.

### How to Accomplish This

In order to accomplish this rating system, we have to record app launches, determine whether we should show the rating prompt or not, and make sure that once it has been touched, it never comes back.

I’ve written a simple helper class to accomplish all of this (admittedly the original version was based on a long forgotten stack overflow answer). I’ve published the code in a Gist, but here is how to use it.

### Track App Launches

In my MainActivity, I setup the rating prompt requirements and track an app launch. In our case, we don’t care how many days it’s been since the user has installed the app, since they can become quite familiar with the app quite quickly. All we care about is that the app has been launched at least 3 times.

```
3
// Set up the app rater and track the launch
AppRater.setPromptRequirements(this, 3, 0);
AppRater.trackAppLaunch(this);
```

### Conditionally Show the Button

Next, in my ShareActivity, I hide the rating button if the AppRater says I should.

```
private void setupRatingButton() {
    if (!AppRater.shouldPrompt(this)) {
        mShareView.hideRateView();
        return;
    }
    ...
}
```

That’s really all there is to it. The button will be hidden until the app has been launched at least 3 times and the user exports a photo.

### The Wiggle

That last bit is wiggling the button the first time it appears. I simply use shared preferences to track whether the button has wiggled before, and wiggle it if not.

```
// Wiggle the rate button
final SharedPreferences prefs = PreferenceManager.getDefaultSharedPreferences(this);
if (!prefs.getBoolean(HAS_WIGGLED_KEY, false)) {
       mShareView.postDelayed(() -> {
            prefs.edit().putBoolean(HAS_WIGGLED_KEY, true).commit();
            mShareView.wiggleWiggleWiggleWiggleWiggleYeah();
        }, 1000);
}
```

### The End

Prompting the user for a rating seems trivial, but it’s important to make sure it is a good experience for the user so you don’t get bad reviews. Here, I’ve shared how we request a rating in Fragment, are there any other tips and tricks that you use in your apps or like seeing in apps you’ve downloaded? Share in the comments.

---


原文地址：[http://ryanharter.com/blog/2014/09/05/kindly-asking-for-ratings-and-reviews/](http://ryanharter.com/blog/2014/09/05/kindly-asking-for-ratings-and-reviews/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)