---
layout: post
title: "Integrating an Android Github repo with Travis Ci with the new Android Plugin"
date: xxxx-xx-xx xx:xx
comments: true
categories: Github Travis Ci
---

#Integrating an Android Github repo with Travis Ci with the new Android Plugin

In my [previous post ](http://gmariotti.blogspot.it/2014/04/integrating-android-github-repo-with.html)I wrote how to integrate an Android Github repo with Travis Ci.

Now we can change something in our **travis.yml**, using the new **Android plugin**.

**Warning:The features described here are still in development!**

All the considerations described in the old post remain valid, but we can update some parts.
First, declare the **new language**:
language: android
With this line, travis provides the Android SDK 22.6.2 with following **preinstalled components**:

* platform-tools
* android-19
* sysimg-19 (ARM)
* android-18
* sysimg-18 (ARM)
* android-17
* sysimg-17 (ARM)
* android-16
* sysimg-16 (ARM)
* android-15
* sysimg-15 (ARM)
* android-10
* extra-android-support
* extra-google-google_play_services
* extra-google-m2repository
* extra-android-m2repository

Here you can find the [updated list](https://github.com/travis-ci/travis-cookbooks/blob/master/ci_environment/android-sdk/attributes/default.rb#L22-L38).

Then we can **add or update**(a.k.a. re-install) some **components** to get the latest minor versions:

```
android:
  components:
    - build-tools-19.0.3
Under the wood the plugin runs the command:
android update sdk --no-ui --all --filter "component-name"

```

The new android plugin can work with gradle. If your project has build.gradle file in the repository root, Gradle will be used to build it. 
Of course you can put your wrapper script in your repository to use it.

You can find more detailed info here.

Here my new travis.yml file (it is just an example!):

```
language: android
android:
  components:
    - build-tools-19.0.3

notifications:
  email: false

before_install:

  # for gradle output style
  - export TERM=dumb

  # environment info
  - ./gradlew -v
  - uname -a

# Let's try to build...
script: ./gradlew clean build

```

Here, the "old" travis.yml file:
language: java

```
jdk: oraclejdk7

env:
  global:
    - ANDROID_SDK_VERSION="r22.6.2"

notifications:
  email: false

before_install:

  # environment info
  - gradle -v
  - uname -a

  # required libs for android build tools
  # Update a system for ia32 libraries
  - sudo apt-get update -qq
  - if [ `uname -m` = x86_64 ]; then sudo apt-get update; fi
  - if [ `uname -m` = x86_64 ]; then sudo apt-get install -qq --force-yes libgd2-xpm ia32-libs ia32-libs-multiarch; fi

  # for gradle output style
  - export TERM=dumb

  # newer version of gradle
  # - wget http://services.gradle.org/distributions/gradle-1.11-bin.zip
  # - unzip -qq gradle-1.11-bin.zip
  # - export GRADLE_HOME=$PWD/gradle-1.11
  # - export PATH=$GRADLE_HOME/bin:$PATH

  # just to test gradle version, against our provided one
  - gradle -v

  # newest android SDK
  - wget http://dl.google.com/android/android-sdk_${ANDROID_SDK_VERSION}-linux.tgz
  - tar -zxf android-sdk_${ANDROID_SDK_VERSION}-linux.tgz
  - export ANDROID_HOME=`pwd`/android-sdk-linux
  - export PATH=${PATH}:${ANDROID_HOME}/tools:${ANDROID_HOME}/platform-tools

  # manually set sdk.dir variable, according to local paths
  - echo "sdk.dir=$ANDROID_HOME" > local.properties

  # Install required components.
  # For a full list, run `android list sdk -a --extended`
  - echo yes | android update sdk --filter platform-tools --no-ui --force > /dev/null
  - echo yes | android update sdk --all --filter build-tools-19.0.3 --no-ui --force > /dev/null
  - echo yes | android update sdk --filter android-19 --no-ui --force > /dev/null
  - echo yes | android update sdk --filter extra-android-support --no-ui --force > /dev/null
  - echo yes | android update sdk --filter extra-android-m2repository --no-ui --force > /dev/null

# Let's try to build...
script: gradle check

```

---


原文地址：[http://gmariotti.blogspot.com/2014/05/integrating-android-github-repo-with.html](http://gmariotti.blogspot.com/2014/05/integrating-android-github-repo-with.html)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)