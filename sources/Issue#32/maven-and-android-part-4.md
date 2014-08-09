---
layout: post
title: "Maven and Android – Part 4"
date: 2014-08-09 13:56
comments: true
categories: Maven
---


#Maven and Android – Part 4

In the continuing series about automating your Android builds using Maven, we’ll look at one of the foundations of Maven which is what allows Maven to automatically download project dependencies for us: repositories.

In previous articles, we have seen this mysterious mechanism that Maven uses to download libraries and plugins automatically. Actually, it is not all that mysterious, Maven is build around repositories, which are either public or private stores of plugins and libraries (or artifacts, as they are known in Maven parlance). There is a large, public repository known as Maven Central Repository, or Central for short, which contains a huge amount of stuff. By default every Maven installation knows about Central, and the configuration that we added to the POM in previous articles was imply providing it with a way of finding the required artifacts within Central. Maven was then able to do the rest for us.

It is possible to browse Central to see what is available by going to http://search.maven.org and clicking “browse”. If you navigate down to com\jayway\maven\plugins\android\generation2\android-maven-plugin\3.2.0 you will find the POMs, sources, jars, and various checksums for the android-maven-plugin that we used earlier. We identified what we wanted in the POM, and Maven was able to retrieve it from Central automatically.

If you watched the Maven console during some of the builds from the previous articles in series, you may have seen these being downloaded during the build, but if you run the build again, then they are not downloaded. This is because Maven also uses a local repository. Whenever a particular artifact is required for the build, Maven first checks the local repository to see if it already has is, if it doesn’t it will download it from a public repository, and install it in the local repository read for next time the build is run.

You can view the local repository by going to your home directory, and looking inside the .m2/repository folder. Inside this is hierarchy very similar to that on Central and, again, you can navigate down to com\jayway\maven\plugins\android\generation2\android-maven-plugin\3.2.0 in order to find the android-maven-plugin again.

Artifacts are identified by three criteria: A group ID, an artifact ID, and a version. The group ID and artifact ID are analogous to the package name and application name that we specify when creating a new Android project, and the version is analogous to the versioning that we put in our Manifest. The group ID allows different organisations to use the same artifact ID independently of each other, for example if both com.stylingandroid and com.google were to create a library named greatlib, the different groups IDs would enable both to share the same artifact ID. The versioning allows a developer to publish strongly versioned artifacts. Other projects which depend on these can specify specific versions to ensure that newer versions which may contain API changes do not break things.

As well as Central, there are also other third party repositories around. If you find that there is a particular artifact that you require which isn’t available in Central, but it is available in another repository, you can simply add a new repository to your POM:

```
<project>
  ...
  <repositories>
    <repository>
      <id>third-party-repo</id>
      <url>http://third-party/repo</url>
    </repository>
  </repositories>
  ...
</project>
```

Finally, you can also create your own repository. This can either be private on your own network, or you can make it public and allow third party access to it. There are a number of free and paid repo implementations around. I have used both Nexus and Artifactory and both are quite easy to get set up.

In the next article we’ll have a look at the Maven build lifecycle and see how interconnected it is with the repositories.

© 2012, [Mark Allison](http://blog.stylingandroid.com/). All rights reserved. This article originally appeared on [Styling Android](http://blog.stylingandroid.com/).

Portions of this page are modifications based on work created and shared by Google and used according to terms described in the Creative Commons 3.0 Attribution License


---


原文地址：[http://blog.stylingandroid.com/archives/1089](http://blog.stylingandroid.com/archives/1089)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)