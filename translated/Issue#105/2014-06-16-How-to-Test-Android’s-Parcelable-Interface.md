---
layout: post
title: "如何使用Android的Parcelable接口"
date: 2014-06-16 18:06
comments: true
categories: Android Parcelable接口
---

Android应用里总看到将数据放到一个Intent或者Bundle传送给另一个组件。我发现传递Java对象比传递多个key/value对更容易维护。如果使用对象传递，当你要对传递的对象进行添加和移除字段时就不用再去修改每个Intent&Bundle。实现对象传递的对象需要继承[Serializable][Serializable]或者[Parcelable][Parcelable]。而我更倾向于使用Parcelable，尽管它需更多代码实现。

最近我陷入了Parcelable对象的问题，为了确认我实现Parcelable的方式是正确的，我使用了单元测试——但我出乎意料的是真机运行Parcelable并没有像测试一样如期运作。二月初的一个晚上，当我在重构代码时，发现从Bundle取出特定的Parcelable实现类会抛出ClassCastExceptions异常。我找不到了这个类型在我代码里什么时候变换了，由于过于相信我的单元测试，导致我认为Parcelable的实现是没有问题。经过几个小时的查询，我发现对Parcelable的单元测试不能保证什么东西。希望该文章能使你避免同样的问题。

以下先是实现Parcelable的简易总结（如果你之前没用过的话，可以一观）。创建Parcelable需要对Parcel进行读写处理，然后使用CREATOR工厂创建Parcel对象。下面的代码很直观，一目了然。请细细观察'Foo'类下面的实现。我通常使用equals()方法来测试这几种字段类型。

![foo implementation][foo implementation]

为了使传递对象的单元测试更有效，你必须重写equals()方法。这样保证所有字段都能测试到。在Java IDE中重写eqeals方法非常简单，用Guava equals helpers方法即可简洁实现。

（有趣问题：为什么Android team不提供Parcel的writeBoolean() and readBoolean()方法？我通常使用int来替代boolean，当然你可以用byte或者String来替代。感谢[StackOverflow][StackOverflow]的回答，给了我们更多如何传递的灵感）

我之前天真的单元测试反映了我实际是如何使用Parcelable对象。保存到Bundle，从其取出并检查取出的跟保存之前的是否相同。

![parcel unit test][parcel unit test]

注：使用下面我之前写的Foo类的CREATOR来测试，上面的单元测试也能通过。而它们的区别是CREATOR返回类型是Bar而不是Foo。这一点很容易忽视，因为返回的Bar也是实现了Parcel，因此不会报任何错误。

![bar instead of foo][bar instead of foo]

确定真正的bug后，我更关心为什么单元测试没有发现这个问题。事实证明，Bundle只有在传递到自身对象时候才会去做序列化/反序列化值。感谢James Wilson提供一个正确的测试方法。

![correct parcel unit test][correct parcel unit test]

事实上，以上代码不会编译成功，因为CREATOR会在编译时候检查返回类型，而不是在运行时候转化。

我推荐你在创建Parcelable对象后都用该方法来测试。

由[Kevin’s blog][Kevin’s blog]发布。

---
原文地址：[http://tech.gilt.com/post/87599117269/how-to-test-androids-parcelable-interface](http://tech.gilt.com/post/87599117269/how-to-test-androids-parcelable-interface)

译者：[whosea](https://github.com/whosea) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)


[Serializable]:http://developer.android.com/reference/java/io/Serializable.html
[Parcelable]:http://developer.android.com/reference/android/os/Parcelable.html
[StackOverflow]:http://stackoverflow.com/questions/6201311/how-to-read-write-a-boolean-when-implementing-the-parcelable-interface

[Kevin’s blog]:http://www.kevinrschultz.com/blog/2014/03/01/how-not-to-test-androids-parcelable-interface/

[foo implementation]:http://awcntt-article-image.qiniudn.com/issue105_tumblr_inline_n6jqrga4w91s17bu5.png
[parcel unit test]:http://awcntt-article-image.qiniudn.com/issue105_tumblr_inline_n6jqs9W3Wg1s17bu5.png
[bar instead of foo]:http://awcntt-article-image.qiniudn.com/issue105_tumblr_inline_n6jqt9Hh8f1s17bu5.png
[correct parcel unit test]:http://awcntt-article-image.qiniudn.com/issue105_tumblr_inline_n6jqu2oZfC1s17bu5.png