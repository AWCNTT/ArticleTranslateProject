---
layout: post
title: "Maven and Android – Part 6"
date: 2014-08-09 14:02
comments: true
categories: Maven
---


#Maven and Android – Part 6

In the [previous article](http://blog.stylingandroid.com/archives/1114) we looked at the Maven build lifecycle. In this concluding part, we’ll have a brief tour of some of the more advanced things that we can do with Maven which may convince you that Mavenising your build can provide you with great benefits.

Maven LogoFirstly, the examples that we hav discussed up to now work well for a single developer: It fine that one developer can install a library to his local repository and then use it in another project, but what about when we have a team? If the library is tightly coupled to the project, then we’d most likely use a compound project as we described in the last article, and each developer would periodically update from source control. However, if we have a loosely coupled library, we could actually deploy this to a private repository. The other developers would simply add the dependency to their POM, and Maven would take change of automatically retrieving it.

You may have noticed that the version names that we have used in the example POMs throughout this series have the test “-SNAPSHOT” appended. This is because Maven supports two kinds of build a SNAPSHOT which is analogous with a “nightly” build – it’s bleeding edge, contains the latest changes, but may be a little unstable as a result. Then there’s a RELEASE build which is a formalised release which should be rather more stable, but won’t have some of the latest features being worked upon.

Around this, Maven supports two kinds of repository: snapshot and release. While these may physically be the same, then can be addressed independently. When you perform a “mvn deploy” this will usually deploy a snapshot version of your artifact to the snapshot repository. However, Maven also has a plugin for making formal releases which is extremely powerful.

Making a release is a two stage process: First you prepare the release, then you perform it. Preparing the release consists of sanity checking your source control, then modifying all of your version strings to “-RELEASE”, committing to source control, then tagging the release in source control. Next it increments your version numbers, and switches back to “-SNAPSHOT” before committing again. This has created a tag in source control representing your release version, and moved on to a new bleeding edge version. At this point, if you have discovered any prolems you can rollback the release to where you were at the start. If everything is OK, you can now perform the release which will check out the tagged version from source control, then build and deploy the release to the release repository.

In order to keep things simple, I have actually omitted some stages which we’ll look at in a moment. The relevant commands for doing this are simply:

[code]
mvn release-prepare
mvn release-rollback
mvn release-perform
[/code]

This is really powerful stuff! It can take a few attempts to get the hang of, and sometimes you may have to manually remove tags from your source control system, but once it’s all working making releases really easy.

Another extremely powerful feature of Maven is the ability to create a “site” for your project using the site plugin. This is a effectively a web site containing documentation about your project. The websites of many Maven plugins (include the [site plugin](http://maven.apache.org/plugins/maven-site-plugin/)) are actually generated automatically. There is a simple markup language in which you can create documentation manually, but there re also a number of reporting plugins which will generate reports which get deployed to the site. Examples of these are a javadoc plugin which will compile and deploy the javadocs from your code to the site; a change plugin which will compile a changelog from your SCM and deploy it to the site; unit test reports; code coverage reports; the list goes on and on. When discussing the release plugin, I mentions that I had omitted some stages for clarity, and one of those is the building and deploying of the site as part of your release process – which can automatically generate release notes.

Some things that are rather more Android-specific are the ability to run your Android tests. There is a Continuous Integration platform named Jenkins which has an Android emulator plugin. This allows you to execute your Android unit tests within a CI server. CI is an integral part of Test Driven Development, and Maven is extremely well suited to automating builds for CI purposes. In a series about Maven it is straying off-topic a little to explore CI, so we’ll leave it there for now – that may be a future article, though!

An alternative to this is to use [Robolectric](http://pivotal.github.com/robolectric/). Corey Leigh Latislaw has written an excellent guide for integrating [IntilliJ IDEA with Maven and Robolectric](http://).

In this final article of this series I have only scratched the surface of the possibilities that Maven opens up for us. There is a good chance that someone has written a plugin to perform a lot of the common tasks that we may want to do. For tasks specific to our project, writing our own Maven plugins is surprisingly easy.

Maven can be daunting when you first try to use it because it works in a different way to many more traditional build technologies such as make and ant. Until you get a understanding of how Maven works it can be difficult to get the hang of, and that often puts people off. However, perseverance can be highly rewarding: When you suddenly realise that you understand what’s happening, and how Maven works there’s no going back. The thought of going back to the aforementioned make and ant will fill you full of dread

© 2012, [Mark Allison](http://blog.stylingandroid.com/). All rights reserved. This article originally appeared on [Styling Android](http://blog.stylingandroid.com/).

Portions of this page are modifications based on work created and shared by Google and used according to terms described in the Creative Commons 3.0 Attribution License


---


原文地址：[http://blog.stylingandroid.com/archives/1114](http://blog.stylingandroid.com/archives/1114)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)