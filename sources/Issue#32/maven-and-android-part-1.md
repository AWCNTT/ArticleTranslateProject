---
layout: post
title: "Maven and Android – Part 1"
date: 2014-08-09 13:41
comments: true
categories: Maven
---

#Maven and Android – Part 1

Apache Maven is a build automation tool which does an awful lot more besides. I have used Maven for a number of years for server-side projects and think that it is a fantastic tool. I have tried it on a number of occasions for automating Android builds but always either hit problems, or just found the Android tools were lacking some of the features that I required and was forced to give up. It was also quite painful to get installed and working correctly. A couple of months ago, I tried it once again and was pleasantly surprised to find that the Android tools for Maven finally seem to have matured. In this series we’ll look at how to get Maven set up for Eclipse and ADT, and explore some of the cool things that Maven can do for us.

Maven LogoAt the time of writing I am running the latest version of Eclipse (Indigo) with ADT 18. If you’re using later versions, then some of the URLs which follow may be different.

If you are running Eclipse Indigo, Maven integration may already be installed. If it isn’t, go to Help|Install new , select “–All Available Sites–” from the “Work with:” drop down. Then type “maven” in to the filter field, and you should see “m2e – Maven Integration for Eclipse” which is, at the time of writing, version 1.0.200.20111228-1245. Install this.

We now have Maven integrated in to Eclipse. When this was installed, it had a version of Maven already included. In my case this was Maven 3.0.2 and it works well. However, if you are working with an project which is already Mavenized, this may require a later version of Maven. Adding this is very simple, go to http://maven.apache.org/download.html and download the version of Maven that you require. Then unpack it to a folder of your choosing and remember it because we’ll need it later. Then, in Eclipse, go to Window|Preferences|Maven|Installations, then click on “add”, then navigate to the folder in which you unpacked your new version of Maven and select it. It will then appear in the list of Maven installations and you can select it to use that instead of the embedded version. There is a plugin that we will use later which requires Maven 3.0.3 or higher, so it is worth downloading the latest version of Maven (3.0.4 at the time of writing) and configuring that in place of the embedded version.

Now we need to add Android support to Maven. We can do this by going to Window|Preferences|Maven|Discovery and clicking “Open Catalog”. When the dialog fully loads select “Android Connector” then click “Finish”. This will add Android support for Maven within Eclipse.

Now that we have the necessary tools installed, we need to select a project that we wish to Mavenize. I’m using the source for the last article on Transparency which you can get from [here](https://github.com/StylingAndroid/Transparency/tree/Part3).

Right click on the Project in Package Explorer in Eclipse, then select Configure|Convert to Maven project. It will pop up a dialog box asking for various parameters. I’ve configured mine as follows:

![Convert](http://awcntt-article-image.qiniudn.com/issue32_convert.png)

One important thing to note is that the “Packaging” drop down contains three possible values: jar, pom, & war. However we can override this by typing in a value, and we enter “apk” here as we and to build an Android APK.

This will create a new file in the top level of your project named “pom.xml”. This is the Maven Project Object Model file, and effectively contains the instructions to maven of how to build your project. At the moment it contains the details that we entered when we converted our project:

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
</project>
```

Now the first thing that you may notice is that the Eclipse build of your project now fails. While we installed some tools which help maven integrate with Eclipse, and the Maven / Eclipse integration work with Android projects, this was all to integrate things in to Eclipse. Maven itself does not know how to build Android app. In then next part of this series we’ll look at how we can use plugins to allow Maven to build Android APKs.


---


原文地址：[http://blog.stylingandroid.com/archives/1067](http://blog.stylingandroid.com/archives/1067)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)