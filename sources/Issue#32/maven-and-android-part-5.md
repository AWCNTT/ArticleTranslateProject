---
layout: post
title: "Maven and Android – Part 5"
date: 2014-08-09 14:02
comments: true
categories: Maven
---


#Maven and Android – Part 5

In the [previous article](http://blog.stylingandroid.com/archives/1089) we had a look at Maven repositories and saw how Maven can automatically retrieve artifacts from public repositories an put them in to our local one. In this article we’ll look at the Maven build lifecycle, and how we can use Maven to put our own artifacts in to repositories.

As well as repositories, an important foundation of Maven is its build lifecycle. The Maven build lifecycle is comprised of a number of phases which are executed in sequence. Each of these phases can have goals attached, and goals are provided either internally, or through plugins. I’m not going to provide a full description of the Maven build lifecycle because there is already an excellent [description](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html) on the Apache Maven Project website. However, in previous articles we have encountered aspects of the build lifecycle. Back in part 2[link](http://blog.stylingandroid.com/archives/1072) of this series we looked briefly at the android-maven-plugin and described how it attaches itself to the Maven build lifecycle. An Android project build is much more complex than a simple Java project build because we have resources to worry about (we have to also run a preprocessor to create the auto generated code for R.java and any aidl interfaces), and we need to convert the JAR file in to an APK. The android-maven-plugin does this for us by attaching its goals to different points of the Maven build lifecycle to perform those tasks in the correct order.

Back in Part 3 of this series we performed a Maven build by selecting “Maven|Install” but, at the time, it was unclear what we were installing. When we invoke Maven either through a command line, or throught Eclipse, in simple terms, we are telling Maven that we want to perform a build up to a certain point of the lifecycle. So, doing a “Maven|Install” in Eclipse (which is the same as “mvn install” from the command line) will perform a build up to and including the “install” phase of the Maven build lifecycle. The net result of this is that it will perform all of the steps necessary to produce an APK, and then install this to our local repository.

You may be wondering why we need to install this to our local repository. This becomes apparent when we consider Android library projects (which can be created simply by using “apklib” instead of “apk” as the package type in the POM). If the library gets installed to the local repository, then we can use it in other projects without having to open / import the library project in to Eclipse.

This leads us on to an interesting point about library projects. Sometimes our library and inal APK are tightly coupled and sometimes they are loosely coupled. For example, if you have an app which has both a free and paid version, and the shared functionality is in a library project, with two APK projects, one for the free version and the other for the paid version. Both provide a wrapper around the common functionality from the library project. These are tightly coupled to the library project because often changes will need to be made to the library project. On the other hand, consider some common functionality in a library project, for example a library to read data from an RSS feed. Once this code is working, it can be used in a number of diverse apps, and will not require modification as often. This is loosely coupled.

Maven allows us to work with both of these scenarios quite well. For a library which is loosely coupled to our APK, we can simple install the library to the local repository, and use a Maven dependency in our APK project to effectively import it. For a tightly coupled library, we can create a compound Maven project which contains both the shared library project and the two APK projects. We do this by creating the three projects as normal projects, and then copy them to a new parent folder, and create a new pom in the parent folder:

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <groupId>com.stylingandroid</groupId>
  <artifactId>app</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging>
 
  <modules>
    <module>shared-library</module>
    <module>free-apk</module>
    <module>paid-apk</module>
  </modules>
</project>
```

The points to notice are the package type of the parent is “pom” which indicates to Maven that this is a parent project; and the three components are listed as modules.

You will also need to add a parent reference to the POM of each of the three existing projects:

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <parent>
    <groupId>com.stylingandroid</groupId>
    <artifactId>app</artifactId>
    <version>1.0-SNAPSHOT</version>
  </parent>
  ...
```

You still need to define any dependencies between the projects as Maven dependencies, but Maven will find the references within the project structure and use the bleeding edge builds rather than loading them from the local repository.

You can now invoke Maven from the parent folder and it will build all of the child modules.

In the concluding article in this series, we’ll look at some of the more advanced things that Maven can do for us, which may act as a convincer that Mavenising your builds can be a really useful thing.

© 2012, [Mark Allison](http://blog.stylingandroid.com/). All rights reserved. This article originally appeared on [Styling Android](http://blog.stylingandroid.com/).

Portions of this page are modifications based on work created and shared by Google and used according to terms described in the Creative Commons 3.0 Attribution License


---


原文地址：[http://blog.stylingandroid.com/archives/1114](http://blog.stylingandroid.com/archives/1114)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)