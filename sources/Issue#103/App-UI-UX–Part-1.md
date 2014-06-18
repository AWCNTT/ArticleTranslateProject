---
layout: post
title: "Part 1 - App界面效果和用户体验"
date: 2014-6-18 15:00
comments: true
categories: App界面效果和用户体验
---

在之前一系列的Bluetooth LE文章中，我们知道如何从Texas Instruments SensorTag读取温度和湿度值并展示出来。从功能看该app运行不错，但由于之前Bluetooth LE的系列文章关注点都在BLE上，而忽视了UI的重要性。所以在这系列文章里我们将使用不同技术来美化app，给用户一个丰富、充满希望和更具有吸引力的用户界面。

快速回顾一下该app的UI架构，目前这一系列的文章到结束都是用同一个项目的代码，为了避免混乱我重新fork新的repo，大家可以从[这里](https://bitbucket.org/StylingAndroid/appuiux/src/d6feddcb87aaf07d4eaafc6bb7150a48492e9dd3/?at=Initial)找到开始源码。

Activity和Service通讯机制负责与SensorTag通讯，而这些交互都隐藏在背后。我们只要知道当Service改变时我们会接收到消息数据，数据达到后就连接到SensorTag。

Activity本身会在两个Fragments中根据Service的状态切换，如果连接上SensorTag，那么DisplayFragment显示，连接失败则DeviceListFragment显示。

我们来关注DisplayFragment。用户打开app第一眼看到的不是DisplayFragment，因为这里（有可能）耗过多的时间，因此在加载期间放上一些好看的UI。该UI虽很实用但代码却枯燥乏味难以阅读。

![display_initial](http://awcntt-article-image.qiniudn.com/issue103_display_initial.png)

我们这里只做垂直的布局（本应该支持所有方向的布局），但是由于只是做个简单的例子，所以暂时只有垂直布局。

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          package="com.stylingandroid.ble">
 
    <uses-permission android:name="android.permission.BLUETOOTH"/>
    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN"/>
 
    <application
        android:allowBackup="true"
        android:icon="@drawable/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme">
        <activity
            android:name="com.stylingandroid.ble.BleActivity"
            android:label="@string/app_name"
            android:screenOrientation="portrait">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
 
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
        <service android:name=".BleService">
        </service>
    </application>
 
</manifest>
```

接下来看看布局，明显我们需要将布局改大，同时视觉上看起来也是享受。我们先移除以前`TextViews`标签的属性，增加字体大小并居中，给`TextViews`周围边框线条设成灰色，再给`FrameLayout`附上蓝色背景。后面一系列的文章我们将做一些有趣的事情：

```xml
<?xml version="1.0" encoding="utf-8"?>
 
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:id="@+id/parent_container"
    android:background="#0000FF"
    android:padding="@dimen/display_margin">
 
    <LinearLayout
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@color/background">
 
        <TextView
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="5"
            android:id="@+id/temperature"
            android:textColor="@color/text"
            tools:text="20°C"
            android:gravity="center"
            android:layout_gravity="center_horizontal"
            android:textSize="@dimen/display_large_text"/>
 
        <View
            android:layout_width="match_parent"
            android:layout_height="1dp"
            android:background="@color/divider" />
 
        <TextView
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="3"
            android:textColor="@color/text"
            android:id="@+id/humidity"
            tools:text="50%"
            android:gravity="center"
            android:layout_gravity="center_horizontal"
            android:textSize="@dimen/display_medium_text"/>
 
    </LinearLayout>
</FrameLayout>
```

这里需要一些资源文件来支持以上布局。首先在res/xml/dimens.xml设置一些尺寸变量的值：

```xml
<resources>
    <dimen name="display_margin">48dp</dimen>
    <dimen name="display_medium_text">48sp</dimen>
    <dimen name="display_large_text">64sp</dimen>
</resources>
```

同样res/xml/colours.xml里也需要设置一些颜色变量的值：

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="text">#A0FFFFFF</color>
    <color name="background">#30000000</color>
    <color name="divider">#40000000</color>
</resources>
```

这里没什么特别复杂的地方，我就不唠叨了。最后基本的布局如下：

![display_layout](http://awcntt-article-image.qiniudn.com/issue103_display_layout.png)

有了基本布局后，我们将在后续文章改善优化它。后续文章里我们将在app自定义字体，以达到更好的效果，并且用静态text标签来替代（之前的“Temperature”和“Humidity”用的标签将会移除）。

本文章的源码——[here](http://code.stylingandroid.com/appuiux/src/72ebcc0463e879f9d6f71df0100891bc9470189a/?at=Part1)

© 2014, Mark Allison. All rights reserved. This article originally appeared on [Styling Android](http://blog.stylingandroid.com/).

---

原文地址：http://blog.stylingandroid.com/archives/2571

译者：[whosea](https://github.com/whosea) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)