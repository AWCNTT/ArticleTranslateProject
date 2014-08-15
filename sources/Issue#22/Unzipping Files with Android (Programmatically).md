---
layout: post
title: "Unzipping Files with Android (Programmatically)"
date: xxxx-xx-xx xx:xx
comments: true
categories: xxxxx(文章关键字，多个关键字用英文空格分割)
---

I wrote an article a few months ago, which walked you through the process of zipping files programmatically with Java - in an Android environment. Due to popular demand, I've decided to write up another article on how to unzip files.

The process is pretty similar, and you could probably combine them both in a single class, but for this example, I made a class called ``Decompress``.

```java
import android.util.Log; 
import java.io.File; 
import java.io.FileInputStream; 
import java.io.FileOutputStream; 
import java.util.zip.ZipEntry; 
import java.util.zip.ZipInputStream; 
 
/** 
 * 
 * @author jon 
 */ 
public class Decompress { 
  private String _zipFile; 
  private String _location; 
 
  public Decompress(String zipFile, String location) { 
    _zipFile = zipFile; 
    _location = location; 
 
    _dirChecker(""); 
  } 
 
  public void unzip() { 
    try  { 
      FileInputStream fin = new FileInputStream(_zipFile); 
      ZipInputStream zin = new ZipInputStream(fin); 
      ZipEntry ze = null; 
      while ((ze = zin.getNextEntry()) != null) { 
        Log.v("Decompress", "Unzipping " + ze.getName()); 
 
        if(ze.isDirectory()) { 
          _dirChecker(ze.getName()); 
        } else { 
          FileOutputStream fout = new FileOutputStream(_location + ze.getName()); 
          for (int c = zin.read(); c != -1; c = zin.read()) { 
            fout.write(c); 
          } 
 
          zin.closeEntry(); 
          fout.close(); 
        } 
         
      } 
      zin.close(); 
    } catch(Exception e) { 
      Log.e("Decompress", "unzip", e); 
    } 
 
  } 
 
  private void _dirChecker(String dir) { 
    File f = new File(_location + dir); 
 
    if(!f.isDirectory()) { 
      f.mkdirs(); 
    } 
  } 
} 
```

Well, that's the class - tested on my Nexus One and works!

Here's an example of how to use it:

```java
String zipFile = Environment.getExternalStorageDirectory() + "/files.zip"; 
String unzipLocation = Environment.getExternalStorageDirectory() + "/unzipped/"; 
 
Decompress d = new Decompress(zipFile, unzipLocation); 
d.unzip(); 

```

That should unzip `files.zip` and put the contents into `/sdcard/unzipped/`, as was specified.

Check out my other article, if you want to find out how to zip files - [Zipping Files with Android (Programmatically)](http://jondev.net/articles/Zipping_Files_with_Android_(Programmatically))


---


原文地址：[http://jondev.net/articles/Unzipping_Files_with_Android_(Programmatically)](http://jondev.net/articles/Unzipping_Files_with_Android_(Programmatically))

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)