---
layout: post
title: "Maven and Android – Part 2"
date: 2014-08-09 13:49
comments: true
categories: Maven
---

#Maven and Android – Part 2

In the [previous article](http://blog.stylingandroid.com/archives/1067) we installed Maven and some Android related tools, and got an existing project converted to a Maven build. We had an error because despite all of the tools we installed, Maven itself does not know how to build Android apps. In this article we’ll fix the build and begin to get an understanding of how Maven works and what benefits it offers us.

Maven LogoAs was explained previously, Maven does not directly know how to build Android apps, and that was the cause of the error that we saw – we asked it to build an APK, but it didn’t know how. Maven is vastly extensible through plugins, and there is a plugin that we can use which does know how to build an APK. All that we need to do is to configure Maven to use that plugin for the build. We do this by modifying the POM:

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
 
    <build>
        <finalName>${project.artifactId}</finalName>
        <sourceDirectory>src</sourceDirectory>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>com.jayway.maven.plugins.android.generation2</groupId>
                    <artifactId>android-maven-plugin</artifactId>
                    <version>3.2.0</version>
                    <extensions>true</extensions>
                </plugin>
            </plugins>
        </pluginManagement>
        <plugins>
            <plugin>
                <groupId>com.jayway.maven.plugins.android.generation2</groupId>
                <artifactId>android-maven-plugin</artifactId>
                <configuration>
                    <sdk>
                        <!-- platform or api level (api level 14 = platform 4.0) -->
                        <platform>14</platform>
                    </sdk>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

Let’s try and break this down a little, and explain what each piece is doing. Firstly:

	<finalName>${project.artifactId}</finalName>
	<sourceDirectory>src</sourceDirectory>

These are just some basic configuration which tells Maven the filename that we want to use for our final APK, and where to find our source files relative to the top level directory of the project. Next we have declare a plugin that is required to perform the build:

```
<pluginManagement>
    <plugins>
        <plugin>
            <groupId>com.jayway.maven.plugins.android.generation2</groupId>
            <artifactId>android-maven-plugin</artifactId>
            <version>3.2.0</version>
            <extensions>true</extensions>
        </plugin>
    </plugins>
</pluginManagement>
```

This tells Maven that there is a dependency on the defined plugin as part of the build. Finally we have:

```
<plugins>
    <plugin>
        <groupId>com.jayway.maven.plugins.android.generation2</groupId>
        <artifactId>android-maven-plugin</artifactId>
        <configuration>
            <sdk>
                <!-- platform or api level (api level 14 = platform 4.0) -->
                <platform>14</platform>
            </sdk>
        </configuration>
    </plugin>
</plugins>
```

This is where we actually configure the plugin that we previously declared. In this case we are specifying that we are building against Android API level 14. And that’s all we need.

I’m sure that some will read this and think that I’ve omitted a couple of things, but I really haven’t. Maven does a whole load of stuff for us quite seamlessly, but an explanation of this is needed to fully understand how powerful Maven can be.

The first thing that really isn’t covered is how does Maven get the `android-maven-plugin`? We haven’t given it a URL or anything. We’ll cover this more later in this series, but Maven retrieves it for us automatically.

The other thing that appears to be missing is how simply specifying this plugin is used to actually do the build. This is all done through the Packaging type of “apk” that we specified. Before we added the plugin declaration, Maven gave an error because it did not know how to build a taget package of “apk”. When it starts, Maven initialises all of its plugins, and android-maven-plugin registers itself with Maven with the appropriate packaging type. Maven now hands control over to the plugin as required.

This is a somewhat simplified version of events as the plugin actually attaches itself to various phases of the Maven build lifecycle.

So now we have an error free build, or do we? While Eclipse is not showing an error anymore, Eclipse is not actually doing a maven build each time, it’s still using its own build process. The error that we saw was because Eclipse could not correctly determine how to perform a build based upon the Maven pom. In the next article we’ll look at how to actually perform a Maven build.

© 2012, [Mark Allison](http://blog.stylingandroid.com/). All rights reserved. This article originally appeared on [Styling Android](http://blog.stylingandroid.com/).

Portions of this page are modifications based on work created and shared by Google and used according to terms described in the Creative Commons 3.0 Attribution License

---


原文地址：[https://github.com/AWCNTT/ArticleTranslateProject](https://github.com/AWCNTT/ArticleTranslateProject)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)