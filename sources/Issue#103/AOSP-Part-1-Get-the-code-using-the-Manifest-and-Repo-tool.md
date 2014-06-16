---
layout: post
title: "文章中文标题xxxx"
date: xxxx-xx-xx xx:xx
comments: true
categories: xxxxx(文章关键字，多个关键字用英文空格分割)
---

With a new job at a new place, I decided to also try a new laptop running a new platform – Mac OS. I always used Windows before that. I have used Linux as a VM and on servers I worked on, but as a primary OS – I sticked with Windows (with no particular reason).  After many recommendations, I decided to give Mac its big break. As one big advantage of Mac over Windows – I could build my own [Android ROM](http://droidlessons.com/what-are-roms-for-android/) (Android operation system software, running on a device), which i’ve wanted to try and do for a long time.

![image](http://awcntt-article-image.qiniudn.com/issue103_wpid-images-21.jpg)

It wasn’t an easy start..

There are many developers who are working on their own Android ROM (not nearly as app developers though..), but the solutions to many of the common problems are scattered across Google Groups, Stackoverflow and some blog posts across the net. Getting my environment to work and make the changes I wanted to do, required research and many trials and errors. I’ll try to sum up some of the important stuff I learned from my experience, to get you up and running. I’ll also share some tips to a better workflow while developing on AOSP. Later on, I’ll post about specific stuff that can be achieved, as adding new system fonts, changing boot animation and even customizing the navigation/status bar. The introduction will be divided into 3 parts, each will get its own blog post:

**Part 1: Get the code using Manifest and repo**. This is the post you’re currently reading. I’ll explain what is a manifest project and how it helps you define your system’s structure. The manifest project is used by the repo tool to download all the source code you need, which will be discussed too.

**Part 2: Build variants**. This part will discuss the different builds you can create. Whether it’s flash-able images, OTA (=Over-The-Air) package and even your own Android SDK. We can also create builds for different devices using different configurations.

**Part 3: Developing with ease.** After learning how to create our new Android flavor, which doesn’t have to be named after a dessert, we can start making our work simple as possible. Building the entire system is unnecessary while making small changes, there are ways speed up the process of getting your code into the device. I’ll also share some of the scripts I wrote to make my life easier.
Before we’ll dive into the mysteries of the Manifest project, let’s discuss some of the prerequisites from our build environment.

###What do we need

Frankly, Google did a pretty good job with the build environment initialization guide. They covered most of the cases that could happen while initializing your environment, addressing different build server OS and Android versions. I have some insights to add on top of those:

####Linux and Mac OS

One important thing that people ignore or assume that is correct, is the java version they use. Make sure the java version you have installed (and use by default) is the one described in the Google’s official guide. Up until recently, the only java version allowed was Java 1.6, but with the latest KitKat editions we can use Java 1.7. Consult the Google’s official AOSP environment initialization guide, to see the correct java version you need to use.

####Mac OS

1. Get Homebrew. Seriously, this will make your life much easier in general and also for this process, where you need to install several packages. This is a very handy tool that every Mac power user should have.

2. As part of the process, you’ll be needing to create a disk image with a specific attributes that the AOSP system requires. Creating the image with enough space is important. They mention that 25GB is a minimum, but they don’t recommend anything else. A full repo can take between 15-30 GB and after building – 20GB more. You might even create an image for other hardware variants (Nexus 7, Nexus 5 etc), which will take extra space. If you plan to create SDKs too, you may want to copy the current SDK outside of the output folder, for compile other apps that uses that. Keeping it in the same disk image will require more space, but will make the moving process much faster each time. My recommendation is using 100GB of space, which gives you enough room for building and playing around.

3. You need to have Xcode installed, including its Command Line Tools. I think you should go ahead and make sure you have that, but if you want to avoid installing a few GBs just for a few commands – you can follow this blog post. I’ll just note that I personally didn’t go this way since I have enough disk space on my laptop, so I didn’t mind installing the whole thing. I’ll also mention that as of this day, where Android 4.4.2 is the latest, there’s a problem using Xcode 5.1. If you have that installed – revert back to 5.0.2.

###Repo


The AOSP source code is a combination (some will say “Synergy”) of ~200 different git repositories. Using the separate repos for different components allows better collaboration and make the entire project more organized. But how do we control that amount of repos? We use the repo tool.

The repo tool is here to help us control the insane amount of git repositories. We can update the entire system in one command, we can start a branch on multiple repositories at once and more. The way repo knows the components it needs to work with, is by initializing it to a Manifest project, which holds the details on all the components we need to work with on this project. More on the Manifest project later on.

####repo init

Initializes the current folder as our project’s root directory, where all the components will be downloaded. There are few important switches to this command:

**-u <manifest project git URL>** – This will determine the manifest project we’ll use, where all the component we need are declared.

**-b <manifest project branch>** – We have the option to use different branches of the manifest project, in order to switch between different presets of our project. For instance, we can use a manifest that has defined a different branch to some of its components, to reflect a new feature we are currently testing and not yet merged into our main branch. Omitting this will use the “master” branch.

**-g <groups to download>** – Using this switch, we can define the component groups we wish to download. Omitting this will use the default group, which has what we need for a standard build. Other groups can be the “tools” group, which consists of the components relevant to the build tools as the ADT, emulator and more. There are also groups to distinguish between the components relevant to a Linux build environment and to a Mac OS one, since we usually don’t need both downloaded.

**-m <manifest xml file>** – choosing the manifest xml file within the manifest project. The default name is “default.xml. Using different manifest XMLs can help us maintain different configurations in the same branch. For example – we can use a manifest xml for testing purposes, which will have additional repositories for diagnostics purposes.

Examples:

`repo init -u git@github.com/udinic/manifest.git`

Initializes the current folder to use a manifest project on the provided url, and use the “master“ branch.

`repo init -u git@github.com/udinic/manifest.git -b new_feature_branch -g default,tools`

Initializes the current folder to use the manifest on the new_feature_branch and sync the “tools” group repositories in addition to the default ones.

`repo init -u git@github.com/udinic/manifest.git -m auto-tests.xml`

Initializes our current folder to use the auto-test.xml manifest file, on the master branch of the manifest project.

More on the Repo tool will be discussed on Part 3, where we’ll use it for our day-to-day development needs.

###The Manifest

A manifest file is an XML file, describing a list of repositories to sync our working directory with.

We can take repositories from different git servers, use different branches for each one of them, gather them into groups for easy management and more. Here’s an example for a manifest xml file:

```xml

	<manifest>
  		<remote  name="aosp"
           fetch="https://android.googlesource.com/"/>
  		<remote  name="udinic"
           fetch="https://github.com/udinic/"/>
  		<default revision="master"
           remote="aosp"
           sync-j="4" />
  		<project path="art" name="platform/art" />
  		<project path="bionic" name="platform/bionic" groups="pdk" />
  		<project path="dalvik" name="platform_dalvik” remote=“udinic”/>
  		<project path="frameworks/base" name="platform_frameworks_base" remote=“udinic" revision=“statusbar_fixes"/>
  		<project path="tools/adt/eclipse" name="platform/tools/adt/eclipse" groups="notdefault,tools" />
	</manifest>
```

Let’s review its structure:

**<remote>** – The remote tag is describing the remote git servers we support pulling repositories from. In this case, we have a remote named aosp, linking to the Google’s aosp git server, and another one named udinic, linking to a Github account of the user “udinic”.

**<project>** – Defines a single repository. These are the main attributes:

**path** – Where the repository will be checked out into, relative to the current working directory.
	
**name** – The name of the project on our git server

**remote** – The name of the remote server where the repository can be found.

**revision** – The branch/tag name we want to checkout.

**group** – The group name(s) for this project. We can declare a set of projects as members of a group, then sync only them. Omitting this attribute makes the project a member of the “default” group.
<default> – defines a default values for attributes we are using, when syncing the system. In this case, we set the default branch to checkout each project on its “master” branch and the default remote as “aosp”. If we omit those attributes when defining a project, the defaults defined here will be used. Other interesting attributes:

	 **sync-j** – The value for this will be the number of threads to use when syncing the system. Parallelization helps get the job done quicker, but could also get the computer or the network stuck. Using 4 threads is what commonly used for syncing.

	**sync-c** – syncing only the current branch/tag from git. This will checkout for each project only the current branch/tag that we specify for it, and not any other branches that exists on the repository. This will help save some space and bandwidth (and also time), but if you’ll need to switch between branches on a specific project later on – you’ll need to fetch it manually.
	
Running a basic repo init command on this manifest file, will sync 4 repositories into the working directory. The “platform/tools/adt/eclipse” project won’t get synced since it’s a member of the “notdefault” and “tools” group. Members of the “notdefault” group can be synced only if one of the groups they belong to are selected to be synced, when initializing the working directory. For example if we’ll use the command repo init **-u <manifest project git url> -g** default,tools we’ll sync all the default repositories and the tools group repositories. Using -g all will sync all the repositories available on the manifest, regardless of the groups they belong to.

	**Note**: Notice the project names I used, for the projects hosted on the Github remote, has no “/“-s as the other projects taken from the aosp remote. The reason for that, is because “\”-s represent folders within the Git repository. Since Github doesn’t really support grouping repositories, I replaced all the “\”-s with “_”-s. That way, I keep my projects organized even when looking on my Github’s account profile.
	
###Syncin’

After running repo init, we’ll have a “.repo” folder in the current working directory. This folder contains the manifest project and a soft link to the current manifest xml file, chosen using the repo init command. The rest of the working directory is empty since we didn’t start the syncing process.
To start syncing our system from the data on the chosen manifest file, we need to run

	repo sync

This command will first checkout the latest manifest file from its own repository, in case it was changed since the last time we ran repo init. After that – the repo tool will go over all the projects in the manifest file and download those we wanted to sync. It’ll sync all the repositories from the groups we selected to sync, checkout the files on the branch we set for each project and from the remote server we declared for it.
This process will take a while. The amount of time is depended on your hardware, the internet connection you have and the way you initialized your repo. This will probably take between 1-2 hours (sometimes, more then that), so you should probably let it work overnight.
You can also sync specific projects defined on the manifest. Just use the following syntax:

	repo sync [PROJ NAME1] [PROJ NAME2] ..

You can control on the number of threads running the sync by adding -j<num-threads> to the commend, thus bypassing the sync-j default value on the manifest. For example: repo sync -j8 will run the sync using 8 threads. As a rule of thumb, it’s good not to cross the number of CPU-cores*2 threads. Using -j alone will maximize the number of threads that can be utilized in your computer. Do that only if you don’t intend using the machine for a while.
From my experience, the syncing process could have fetching errors. This is relatively common since we’re downloading a tremendous amount of data, and network issues are no strangers in our world. Running repo sync again will fetch the rest of the missing pieces. Sometimes, error could occur because of the parallelization of the process. If you see errors that relates to access denied or looking for files that aren’t exist yet – using -j1 in the repo sync command will use only 1 thread, and will sync the repositories by the correct order that avoids dependency issues.

###Using a Mirror

If you need to sync the AOSP to different computers on the same network, you can checkout AOSP as a mirror and then sync all the computers with that. This will save bandwidth and time (lots of it!).

To initialize your mirror, you need to initialize your system with a mirroring manifest and the —mirror switch on the repo init commend. The mirroring manifest is very similar to the regular manifest, but with small changes. Here is the commands to sync an AOSP mirror from Google’s servers:

	repo init -u` https://android.googlesource.com/mirror/manifest —mirror
	repo sync

After downloading the mirror, you can initialize other clients using this:

	repo init -u <path to mirror>/platform/manifest.git <other repo init options>
	repo sync
	
If you want to create your own mirroring manifest, there’s a link to a relevant guide on the bottom of this post.

###Ready..let’s do this!

I [created a manifest project](https://github.com/Udinic/ucmod_manifest/blob/master/default.xml) for the purpose of this series of posts. It’s a copy of the default AOSP one, with an addition of a remote git server to my Github account. Currently all the projects are directed to the AOSP git server, but as we progress with this series – we’ll add projects to my Github account and we’ll build our own ROM together, named “ucmod” (udinic-mod).

To initialize your environment with my manifest, use these commands:

	repo init -u https://github.com/Udinic/ucmod_manifest.git
	repo sync
	
..and we wait…

After the code has been downloaded, we have a lot of repositories in our working directory. Going into one of them and running git status will reveal that the repository is in a “detached head” mode:

 	$ git status
	# HEAD detached at 752e389 nothing to commit, working directory clean

This means that we are currently not on a specific branch, but our files are checked out to a specific commit, as our manifest has directed to. This was intended to avoid doing changes on the code without intentions. We’ll discuss how to start working with the repositories on Part 3 of this introduction.
On the next part, we’ll discuss how to take all that code and crunch it into a beautiful package we can install on a device. We’ll also consider different build configurations and see how we can make different “flavors” of AOSP buids.

###Further reading

[Google’s official Guide](http://source.android.com/source/building.html) – This is Google’s official guide to get you started with AOSP development. It’s pretty good as I mentioned earlier and should be used in conjunction with this post to get started.

[Mirroring AOSP](https://groups.google.com/forum/#!msg/android-building/2gLwqjlQq3A/-hmgahe1YcQJ) – The first post from (formally) one of Google’s AOSP main maintainers about the subject.
Mirroring CyanogenMod – This post helps creating a mirroring manifest for the CyanogenMod rom. This could help understand how to create a mirroring manifest for your own ROM.

[AOSP Folders structure](http:/) – Now you have about 200 new repositories in your computer, would like to know more about them and what they do?

---


原文地址：[http://udinic.wordpress.com/2014/05/24/aosp-part-1-get-the-code-using-the-manifest-and-repo/](http://udinic.wordpress.com/2014/05/24/aosp-part-1-get-the-code-using-the-manifest-and-repo/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)