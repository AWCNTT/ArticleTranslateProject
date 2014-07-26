---
layout: post
title: "Android 小贴士: 使用Renderscript方法来模糊图片效率高"
date: 2014-5-29 11:30
comments: true
categories: Android Renderscript 图片模糊效果
---
图片模糊效果是许多开发者需要实现的效果，然而实现它需要一番时间和功夫。还有，由于需要对图像做很多处理，从CUP和内存使用方面看，如果没有合适的编码是非常痛苦的。

这里使用**Renderscript**来模糊图像是非常快和有效的解决方法。

从API11（Honeycomb）提供后，Renderscript 就允许利用GPU加速，它在3D渲染和计算处理方面很高效。
Renderscript 是一个非常复杂和铰接式的框架，它允许深入的配置和使用1999年C语言来编码，这使得它可移植、性能高和易用性强。

然而，自从API (4.2.2) 发布后，Renderscript提供了一系列内置的函数，它们操作方便——被叫做**内部函数**。
内部函数是预先写好的脚本，不用写任何Renderscript代码就能执行模糊、混合、矩阵卷积等更多的操作。

这里我写了个**对图像进行模糊**的简单和高效例子。

```java
    public Bitmap blurBitmap(Bitmap bitmap){
        //创建一个跟原模糊图片同样大小的空图像
        Bitmap outBitmap = Bitmap.createBitmap(bitmap.getWidth(), bitmap.getHeight(), Config.ARGB_8888);
        //实例化Renderscript
        RenderScript rs = RenderScript.create(getApplicationContext());
        //使用Renderscript创建一个内部模糊化脚本
        ScriptIntrinsicBlur blurScript = ScriptIntrinsicBlur.create(rs, Element.U8_4(rs));
        //在Renderscript里面给in/out图像分配一个in/out的区域
        Allocation allIn = Allocation.createFromBitmap(rs, bitmap);
        Allocation allOut = Allocation.createFromBitmap(rs, outBitmap);
        //设置模糊半径
        blurScript.setRadius(25.f);
        //执行Renderscript
        blurScript.setInput(allIn);
        blurScript.forEach(allOut);
        //在out区域里面复制最终处理好的图像给outBitmap
        allOut.copyTo(outBitmap);
        //回收原始图片
        bitmap.recycle();
        //完成后销毁Renderscript
        rs.destroy();

        return outBitmap;
    }
```

最后，模糊图像出现了！ :+1:

![image](http://awcntt-article-image.qiniudn.com/issue103_blur.png)

谨记：使用以上代码需要在API≥17(4.2.2)上运行

以下是该方法的要点：

[https://gist.github.com/Mariuxtheone/903c35b4927c0df18cf8](https://gist.github.com/Mariuxtheone/903c35b4927c0df18cf8)

如果你想发现更多的**内部函数**，请查看Android Developers Blog

[http://android-developers.blogspot.it/2013/08/renderscript-intrinsics.html](http://android-developers.blogspot.it/2013/08/renderscript-intrinsics.html)

如果你想知道更多关于**Renderscript**，请点击：

[http://android-developers.blogspot.it/2011/02/introducing-renderscript.html](http://android-developers.blogspot.it/2011/02/introducing-renderscript.html)

[http://android-developers.blogspot.it/2011/03/renderscript.html](http://android-developers.blogspot.it/2011/03/renderscript.html)

-------------------------------------------------------------------------------

原文地址：[https://plus.google.com/+MarioViviani/posts/fhuzYkji9zz](https://plus.google.com/+MarioViviani/posts/fhuzYkji9zz)

译者：[whosea](https://github.com/whosea) 校对：[inferjay](https://github.com/inferjay)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)