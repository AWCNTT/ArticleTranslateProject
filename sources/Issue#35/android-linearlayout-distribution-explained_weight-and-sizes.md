---
layout: post
title: "Android, LinearLayout Distribution Explained (weight and sizes)"
date: 2014-08-15 17:36
comments: true
categories: LinearLayout
---

# Android, LinearLayout Distribution Explained (weight and sizes)

So this is one of the most bothering issues I have faced during my experience in Android. Besides it has been difficult to find a solution through the internet and in Android Developers it is not as detailed as expected so I feel that this post shall give you a hand to all of you who are still struggling.

I will focus on the main problem as I am sure all of you are familiar with the basic theory.

**The problem**: How in the world can I control the distribution of the elements in a LinearLayout? Yes, I have seen some examples using widths and heights of  0 dip and weights but, what does that mean exactly?

**Weight**: This has been the way of the Android team to achieve proportional distribution of elements within a LinearLayout. Others did that by using the classic percentages which might be more straightforward. Anyway, the idea here is the same with the only difference that weights are factors and percentages are…percentages. Let’s use a simple example:

Say you have a horizontal LinearLayout and you need to put 2 buttons and an image inside so that the buttons take the half of the space:

![image](http://awcntt-article-image.qiniudn.com/issue35_android-linear1.jpg)

This layout from a percentage layout system may have the following configuration (W=weight/wi=width):

Button A: wi= 25% / Button B: wi= 25% / Image: wi= 50%

While in Android:

Button A: W=1 / Button B: W=1 / Image: W=2 (wi for all = 0 dip)

The conversion is the following % = item_weight / weight_sum (Button A: % = 1/4 = 0,25 = 25%).

Of course, if we don’t specify any weight for one of our elements it’ll behave as expected: wrap_content/fill_parent. (Examples below)

Crystal-clear so far?

But in Android there is another variable which is part of the game, and specially the combination of the former and the latter (weight and size).

**Width and height**: As you may know well, there are three values that are commonly used here (wrap_content, match_parent/fill_parent or a fixed size). I will obviate the explanation of these concepts when they are used just to determine width and height. Instead, let’s see what happens when we use them along with weight.

As you have seen in the previous example, if we set to 0 dip the value of the dimension which we are playing with (width in this example) we are telling the system to not to take care about the content but to focus on the whole size of the element, having an equitable distribution of our elements according to our weight values. This is the most common way to organize your layout.

Nevertheless, there is another possibility by using a combination of wrap_content and weight. It may result tricky but the key point is that with this configuration, the system will only take care about the free space left to distribute it according to the specified weight. Our example will help us with that. Note that I did not put the same text in the buttons, and check that the free space left by them is not the same since the text of the first button is longer. What it will happen if we change our width values to “wrap_content” is that our buttons may look longer since there have to be the same free space for every of our elements according to their weight. So that:

Button A: W=1, wi=wrap_content / Button B: W=1, wi=wrap_content / Image: W=2, wi = 0 dip

![image](http://awcntt-article-image.qiniudn.com/issue35_android-linear_wrap.jpg)

To make it clearer I’m sharing a template explaining the concept of distributable space:

![image](http://awcntt-article-image.qiniudn.com/issue35_linearlayout-logic_exp.jpg)

 And some examples with different common configurations:

![image](http://awcntt-article-image.qiniudn.com/issue35_linearlayout-logic1.jpg)

Hope it helps!

PD.: Before explaining it, I had to understand it. I did it thanks to this post:

References: [Android Tales: How weight in LinearLayout works](http://android.amberfog.com/?p=328)

---


原文地址：[http://ugiagonzalez.com/2012/01/19/android-linearlayout-distribution-explained-weight-and-sizes/](http://ugiagonzalez.com/2012/01/19/android-linearlayout-distribution-explained-weight-and-sizes/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)