---
layout: post
title: "Zipping Files with Android (Programmatically)"
date: xxxx-xx-xx xx:xx
comments: true
categories: xxxxx(文章关键字，多个关键字用英文空格分割)
---

One majorly annoying issue that I stumbled upon, was the fact that I couldn't send multiple attachments using Intents to the Google Mail app. The quickest way around that was of course to compress all of the files into one (ZIP).

After searching around online, I didn't really find much on zipping files on your Android device - most of the articles were for standard java applications, which assumed that all your files were in the current directory that you wanted to zip.

So, I used what I could and whipped up my own wrapper class that allows you to easily zip files in Android!

Here is the class:

```java
import android.util.Log; 
import java.io.BufferedInputStream; 
import java.io.BufferedOutputStream; 
import java.io.FileInputStream; 
import java.io.FileOutputStream; 
import java.util.zip.ZipEntry; 
import java.util.zip.ZipOutputStream; 
 
 
public class Compress { 
  private static final int BUFFER = 2048; 
 
  private String[] _files; 
  private String _zipFile; 
 
  public Compress(String[] files, String zipFile) { 
    _files = files; 
    _zipFile = zipFile; 
  } 
 
  public void zip() { 
    try  { 
      BufferedInputStream origin = null; 
      FileOutputStream dest = new FileOutputStream(_zipFile); 
 
      ZipOutputStream out = new ZipOutputStream(new BufferedOutputStream(dest)); 
 
      byte data[] = new byte[BUFFER]; 
 
      for(int i=0; i < _files.length; i++) { 
        Log.v("Compress", "Adding: " + _files[i]); 
        FileInputStream fi = new FileInputStream(_files[i]); 
        origin = new BufferedInputStream(fi, BUFFER); 
        ZipEntry entry = new ZipEntry(_files[i].substring(_files[i].lastIndexOf("/") + 1)); 
        out.putNextEntry(entry); 
        int count; 
        while ((count = origin.read(data, 0, BUFFER)) != -1) { 
          out.write(data, 0, count); 
        } 
        origin.close(); 
      } 
 
      out.close(); 
    } catch(Exception e) { 
      e.printStackTrace(); 
    } 
 
  } 
 
} 
```

If that all makes sense to you already, then you probably won't be interested in my explanations below, otherwise, keep reading. :)

```java
private static final int BUFFER = 2048; 
 
private String[] _files; 
private String _zipFile; 
```

These are the properties that I've declared for the class.

The first is the BUFFER (for reading the data and writing it to the zip stream), second is the _files array, which will hold all the files (path as well) that will be zipped up, and the third is the name of the zip file (path as well).

```java
public Compress(String[] files, String zipFile) { 
  _files = files; 
  _zipFile = zipFile; 
} 
```

This is the constructor, which is the first thing that gets called when instantiating the class - you'll pass it an array of the files you wish to zip, and a string of what the name of the final zip file will be. It then saves this data in the properties, to be used when you call the zip method.

```java
BufferedInputStream origin = null; 
FileOutputStream dest = new FileOutputStream(_zipFile); 
 
ZipOutputStream out = new ZipOutputStream(new BufferedOutputStream(dest)); 
 
byte data[] = new byte[BUFFER]; 
```

Next we move into the zip method - the first things we do is setup our BufferedInputStream, which we'll be using to read the data from the file input stream (each file in the files array).

The FileOutputStream creates the zip file, and sets up the stream, which we then pass to the ZipOutputStream for writing to.

If you want more in depth details about each of these objects (classes), then do a quick search for FileOutputStream or ZipOutputStream in Google.

```java
for(int i=0; i < _files.length; i++) { 
  Log.v("Compress", "Adding: " + _files[i]); 
 
  FileInputStream fi = new FileInputStream(_files[i]); 
  origin = new BufferedInputStream(fi, BUFFER); 
   
  ZipEntry entry = new ZipEntry(_files[i].substring(_files[i].lastIndexOf("/") + 1)); 
  out.putNextEntry(entry); 
 
  int count; 
  while ((count = origin.read(data, 0, BUFFER)) != -1) { 
    out.write(data, 0, count); 
  } 
   
  origin.close(); 
} 
 
out.close(); 
```

And here we have the main loop, which will go through each file in the _files array and compress each one into the zip file.

Here we first setup a FileInputStream for reading the files - the BufferedInputStream will manage the amount of data that it'll be reading at a time (based on the BUFFER).

The ZipEntry is used for adding the actual file/directory structure - for example, if you add '/mnt/sdcard/data/myzip.zip', it would actually create that directory structure in the zip file, so if you want to just add the file into the root of the zip, you'll need to parse it out. Personally, I just used substring, and lastIndexOf to grab the file at the end.

Once the entry is set, you can add it to the zip output stream using out.putNextEntry.

Now we have another loop, which will be used for reading the data, 2048 bytes at a time, and write it to the zip output stream (to the new entry location).

Finally, we clean up - close the streams.

I hope that made sense, and if I can change anything to make it more understandable, please let me know.

Check out my other article, if you want to find out how to unzip files - [Unzipping Files with Android (Programmatically)](http://jondev.net/articles/Unzipping_Files_with_Android_(Programmatically))


---


原文地址：[http://www.jondev.net/articles/Zipping_Files_with_Android_%28Programmatically%29](http://www.jondev.net/articles/Zipping_Files_with_Android_%28Programmatically%29)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)