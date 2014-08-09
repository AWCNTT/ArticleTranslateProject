---
layout: post
title: "Maven and Android – Part 3"
date: 2014-08-09 13:56
comments: true
categories: Maven
---


#Maven and Android – Part 3

In the previous article looked at some errors that were occurring in our Maven build of an Android project. At the end it appeared that everything was now working, but in fact it was not. In this article we’ll look at how the Eclipse and Maven build processes work together in parallel.

Maven LogoMaven is a tool whose function, in part, is to automate our project builds, but Eclipse will also build our project for us. In the previous article, we configured Maven well enough so that Eclipse could determine how to perform its own build using the settings from the Maven POM, but we have not yet performed a Maven build. So let’s actually do a Maven build by right clicking on the project in Project Explorer, then selecting “Run As|Maven Install” (we’ll cover the Maven build lifecycle later on in the series, and why we’re specifying “Install” here will bcome clear later on).

This fails with an error stating that it could not find the Android SDK and offers suggestions of how this can be configured. Eclipse was already set with Java so did not fall foul of this issue. The easy way to do this is to add a to the plugin configuration (obviously you will need to use the location in which you have installed the Android SDK here):

```
<plugins>
    <plugin>
        <groupId>com.jayway.maven.plugins.android.generation2</groupId>
        <artifactId>android-maven-plugin</artifactId>
        <configuration>
            <sdk>
                <!-- platform or api level (api level 14 = platform 4.0) -->
                <platform>14</platform>
                <path>C:\android-sdk</path>
            </sdk>
        </configuration>
    </plugin>
</plugins>
```

If you now run “Maven Install” again, it should overcome this error. You may now get an error saying that it could not load “tools.jar”. If so, you need to ensure that you have a JDK installed and not just a JRE. Then go to Window|Preferences|Java|Installed JREs and add a JRE from within your JDK (i.e. the JDK folder will contain a “jre” folder, selec this), and set this as your default JRE.

The next error that you will see are some compiler errors:

```
[code]
[ERROR] COMPILATION ERROR :
[INFO] -------------------------------------------------------------
[ERROR] C:\Users\Mark\StylingAndroid\Transparency\src\com\stylingandroid\TransparencyActivity.java:[3,18] package android.app does not exist
[ERROR] C:\Users\Mark\StylingAndroid\Transparency\src\com\stylingandroid\TransparencyActivity.java:[4,17] package android.os does not exist
[ERROR] C:\Users\Mark\StylingAndroid\Transparency\src\com\stylingandroid\TransparencyActivity.java:[6,42] cannot find symbol
symbol: class Activity
public class TransparencyActivity extends Activity {
[/code]
```

This is because we are trying to compile code which is dependent on the Android runtime libraries, but the compiler doesn’t know about them. Having ADT installed and configured within Eclipse was, again, enough for Eclipse to build correctly, but not maven. Oonce again we can use this mysterious mechanism that we saw in the previous article which allows Maven to automatically retrieve this for us:

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.stylingandroid.blog</groupId>
    <artifactId>Transparency</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>apk</packaging>
    <name>Transparency</name>
    <description>Sample code for article on transparency</description>
 
    <dependencies>
        <dependency>
            <groupId>com.google.android</groupId>
            <artifactId>android</artifactId>
            <version>4.0.1.2</version>
            <scope>provided</scope>
        </dependency>-
    </dependencies>
    <build>
    .
    .
    .
    </build>
</project>
```

This will download a 4.0 runtime library which we can compile against. provided tells Maven that these libraries will be provided at runtime, so we should not package them up as part of the final APK.

We should now have a fully working build.

In the next part of this series we’ll look at this mysterious mechanism that Maven uses to automatically download stuff for us.

© 2012, [Mark Allison](http://blog.stylingandroid.com/). All rights reserved. This article originally appeared on [Styling Android](http://blog.stylingandroid.com/).

Portions of this page are modifications based on work created and shared by Google and used according to terms described in the Creative Commons 3.0 Attribution License


---


原文地址：[http://blog.stylingandroid.com/archives/1088](http://blog.stylingandroid.com/archives/1088)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)