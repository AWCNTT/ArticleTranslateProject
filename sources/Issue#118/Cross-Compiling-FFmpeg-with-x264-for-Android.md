---
layout: post
title: "Cross Compiling FFmpeg with x264 for Android"
date: 2014-09-12 22:47
comments: true
categories: FFmpeg
---

Recently we were working on a feature where we had to combine an image and audio to create a video on mobile devices. In iOS this can be done using AVAssetExportSessionthough – for detail see this [link](https://developer.apple.com/library/ios/documentation/AVFoundation/Reference/AVAssetExportSession_Class/Reference/Reference.html) However, we could not find any native solution for this problem in Android.

FFmpeg is one such tool to tackle this problem but it is not available for Android officially. We tried few existing Android ports of FFmpeg but they were either outdated or didn’t work for us. So we planned to fold our sleeves and compile the library for Android.

Compiling libraries on Linux system is a fairly common task, download the source code of the library and run three commands:

```
./configure 
make 
make install
```

As we wanted to use the FFmpeg library on Android devices, we can’t just use the executable generated on the Linux machine directly because Android devices have different CPU architecture, different instruction sets and modified Linux kernel (OS). So we needed to cross compile FFmpeg library for Android

**Cross Compilation ?**

The process of building executable binaries on one machine, and running them on another machine when the CPU architecture or the Operating System are different is called “cross compilation”.

A special compiler is needed for doing cross compilation and is called “cross compiler”, and sometimes just “toolchain”. Cross compiler is necessary to compile for different platforms from one machine.

We do cross compilation because the platform for which we are compiling could be infeasible for a compiler to run on, such as for a microcontroller of an embedded system because those systems contain no operating system.

**Compiling FFmpeg for Android:**

Android NDK comes with a cross compiler. On Linux machines you can locate it at following path

```
[Android-directory]/ndk/toolchains/arm-linux-androideabi-4.8/prebuilt/linux-x86_64/bin/
```

First we configured x264 library so that we can include x264 encoders with FFmpeg binary.

```
./configure \
--cross-prefix=arm-linux-androideabi- \
--sysroot="$NDK_SYSROOT" \
--host=arm-linux \
--enable-pic \
--enable-static \
--disable-cli
```
See the explanation to some of the options below:

```
--sysroot=”Android/ndk/platforms/$ANDROID_API_VERSION/arch-arm”
```

sysroot option provides the logical root directory for headers and libraries. This is useful when you don’t want the headers/libraries in the standard paths affect your build. As we want to use Android cross compiler so we need to set the path of Android cross compiler as sysroot.

```
--cross-prefix=arm-linux-androideabi-
```

cross prefix used for locating compilation tools in PATH env variable e.g in place of gcc it will use arm-linux-androideabi-gcc

```
--host=arm-linux
```

host system for which we are compiling. Here we are compiling for 2 different Android architectures namely armv7 and armv7-a neon

```
--enable-pic
```

position-independent code commonly used for shared libraries

```
--enable-static
```

build static library

Then we simply ran the make command using -j option to utilise all cores available

```
make -j$(nproc)
```

Then we configured FFmpeg for armv7-a neon devices

```
./configure \
--target-os=linux \
--cross-prefix=arm-linux-androideabi- \
--arch=arm \
--cpu=armv7-a \
--sysroot="$NDK_SYSROOT" \
--disable-avdevice \
--disable-decoder=h264_vdpau \
--enable-libx264 \
--enable-gpl \
--prefix=../build/armeabi-v7a-neon \
--extra-cflags='-I../x264 -DANDROID -I${ANDROID_NDK_ROOT_PATH}/sources/cxx-stl/system/include -march=armv7-a -mfloat-abi=softfp -mfpu=neon' \
--extra-ldflags='-L../x264 -Wl,--fix-cortex-a8 -L../android-libs -Wl,-rpath-link,../android-libs' \
--extra-cxxflags='-Wno-multichar -fno-exceptions -fno-rtti'
```

See the explanation to some of the options below:

```
--target-os=linux
```

since we are compiling it on linux

```
--arch=arm
```

architecture we are compiling for

```
--cpu=armv7-a
```

cpu we are compiling for

```
--enable-libx264
```

enabling x264 encoders

```
--enable-gpl
```

enabling gpl license required to include x264 lib (by default FFmpeg is lgpl licensed)

```
--prefix=../build/armeabi-v7a-neon
```

setting build prefix path

```
--extra-cflags='-I../x264 -DANDROID -I${ANDROID_NDK_ROOT_PATH}/sources/cxx-stl/system/include -march=armv7-a -mfloat-abi=softfp -mfpu=neon'
```

environment/makefile variables to be passed for compilation and included x264 library

**Some of the commands we used after successfully compiling FFmpeg for Android:**

```
ffmpeg -y -i [INPUT FILE PATH] -c:a copy -c:v libx264 -preset:v ultrafast -profile:v baseline -level 3.0 -r 30 [OUTPUT FILE PATH];
```

converts video to x264 encoding with frame rate 30 and setting video profile to baseline 3.0

```
ffmpeg -y -i [INPUT FILE PATH] -c copy -bsf:v h264_mp4toannexb -f mpegts [OUTPUT FILE PATH]
```

converts video to ts(transport) stream, which we can further use to merge multiple videos

I wrote a shell script for the same, follow the steps in readme to compile it yourself. [https://github.com/hiteshsondhi88/ffmpeg-android/](https://github.com/hiteshsondhi88/ffmpeg-android/)

---


原文地址：[http://vinsol.com/blog/2014/07/30/cross-compiling-ffmpeg-with-x264-for-android/](http://vinsol.com/blog/2014/07/30/cross-compiling-ffmpeg-with-x264-for-android/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)