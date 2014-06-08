---
layout: post
title: "Android用自定义Shapes实现阴影效果"
date: 2014-5-29 10:30
comments: true
categories: Android 阴影效果 自定义Shapes
---

给自定义View的形状创建阴影是很简单的。

首先创建一个阴影层作为背景，然后在其上面放置一层内容层。
当然你可以在这一阴影层创建一些阴影效果。

For example：

```xml
	<selector xmlns:android="http://schemas.android.com/apk/res/android">
	    <item>
	        <layer-list>
	            <item android:bottom="4dp" android:bottom="4dp">	 
	            <!-- SHADOW LAYER -->
	            </item>
	            <item android:left="4dp" android:top="4dp">
	            <!-- CONTENT LAYER-->
	            </item>
	        </layer-list>
	    </item>
	</selector>
```

通过使用*top*，*bottom*，*right*，*left*参数来控制阴影的方向和大小

![shadow direction and size](http://awcntt-article-image.qiniudn.com/issue103_shadow_shapes1.png)

这里我们用[WhoSaid](https://play.google.com/store/apps/details?id=es.slothdevelopers.whosaidit)应用举例

![image](http://awcntt-article-image.qiniudn.com/issue103_custom_shadow_shape.png)

```xml
	<?xml version="1.0" encoding="utf-8"?>
 
	<selector xmlns:android="http://schemas.android.com/apk/res/android">
	    <item android:state_pressed="true">
	        <layer-list>
	            <item android:left="4dp" android:top="4dp">
	                <shape>
	                    <solid android:color="#ff58bb52" />
	                    <corners android:radius="30dip"/>
	                </shape>
	            </item>
	        </layer-list>
	    </item>
	    <item>
	        <layer-list>
	            <!-- SHADOW LAYER -->
	            <item android:left="4dp" android:top="4dp">
	                <shape>
	                    <solid android:color="#66000000" />
	                    <corners android:radius="30dip"/>
	                </shape>
	            </item>
	            <!-- CONTENT LAYER -->
	            <item android:bottom="4dp" android:right="4dp">
	                <shape>
	                    <solid android:color="#ff58bb52" />
	                    <corners android:radius="30dip"/>
	                </shape>
	            </item>
	        </layer-list>
	    </item>
	</selector>
```

以上代码，当我们按下按钮时它会先移除阴影层以提高按下的反馈效果

谨记：**你可以添加距离和颜色来实现最好的效果**。比如你可以给你的阴影层添加渐变

-------------------------------------------------------------------------------

原文地址: [http://slothdevelopers.wordpress.com/2014/05/22/shadow-effect-with-custom-shapes/](http://slothdevelopers.wordpress.com/2014/05/22/shadow-effect-with-custom-shapes/)

译者：[whosea](https://github.com/whosea) 译者：[inferjay](https://github.com/inferjay)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)