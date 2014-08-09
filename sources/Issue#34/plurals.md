---
layout: post
title: "Plurals"
date: 2014-08-09 16:41
comments: true
categories: Plurals
---

#Plurals

When writing software, it is often the tasks that appear trivial which can prove to be rather problematic. In this article we’ll have a look at creating plural strings which, on the surface looks easy, but can cause some unforeseen problems when it comes to actually implementing it.

Let’s start with a really simple example. We want to create a string detailing a number of items. When there is a single item, we want to display “1 item”; otherwise we want to display “x items”. This can be achieved quite easily:

```
String.format( 
    value == 1 ? "%d item " : "%d items ",
    value );
```

This code does precisely what we need. However we can hit problems if we need to internationalise our app so that we display a string localised to the language setting of the device. To those developers like me for whom English is their first and only language, this seems rather easy. Android provides internationalisation through its resource management framework which allows us to simply provide strings.xml for each language that we need to support. We just look up the string by resource ID, and the OS will take care of returning the correct language based upon the device localisation settings.

While this is technically correct, there is one major flaw: Not all languages follow the same pluralisation rules as English. Our code assumes that all languages use one form of the noun (in our example “item”) when there is only one object, and a second form (in our example “items”) for all other cases. While this is true for some other languages, it is certainly not true for many others.

If we now look back at the earlier code we can see that we’ll need to change the conditional logic to cope with different plural rules for different languages. But hang on a second – doesn’t this seem a bit wrong? We’re not having to worry about getting the correct translation, but we then have to determine the language so we can apply the correct pluralisation rules.

Fortunately we do not have to do this because there is actually a pluralisation engine built in to Android resource management which will do this for us. We can use this by defining a plural definition in strings.xml, or plurals.xml, if you prefer:

```
<plurals name="items">
    <item quantity="zero">No items</item>
    <item quantity="one">1 item</item>
    <item quantity="other">%d items</item>
</plurals>
```

Notice how we also define a special form for zero items, as well here. For full details of the quantity types that can be specified please refer to the official documentation.

Once defined we can either lookup the correct form for a given value and perform the formatting ourselves:

```
String fmt = getResources().getQuantityString( resource, value );
String out = String.format( fmt, value );
```

Or we can get a fully formatted string:

```
String out = getResources().getQuantityString( resource, value, value );
```

As a final example, let’s use a plurals definition to create an age string based upon the rules defined here. In res/values/strings.xml:

```
<plurals name="age">
    <item quantity="one">1 year old</item>
    <item quantity="other">%d years old</item>
</plurals>
```

and in res/values-ru/strings.xml:

```
<plurals name="age">
    <item quantity="one">1 лет</item>
    <item quantity="few">%d года</item>
    <item quantity="other">%d лет</item>
</plurals>
```

If we set the device to English we’ll get:

	1 year old 18 years old 23 years old

but if we switch to Russian we’ll get:

	1 лет 18 года 23 лет

This being a technical blog I have not delved in to some of the complexities of pluralisation in specific languages, but really given an overview of the tools which are available within Android to handle much of the burden of pluralising strings. Source code demonstrating the techniques discussed in this article can be found [here](https://github.com/StylingAndroid/Plurals).

N.B. Since this article was written, Wolfram Rittmeyer has published an [article on plurals on](http://www.grokkingandroid.com/wrapping-your-head-around-androids-plurals) Grokking Andorid which contains some information on some of the pitfalls with using plurals.

© 2012, Mark Allison. All rights reserved. This article originally appeared on Styling Android.

Portions of this page are modifications based on work created and shared by Google and used according to terms described in the Creative Commons 3.0 Attribution License


---


原文地址：[http://blog.stylingandroid.com/archives/1136](http://blog.stylingandroid.com/archives/1136)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)