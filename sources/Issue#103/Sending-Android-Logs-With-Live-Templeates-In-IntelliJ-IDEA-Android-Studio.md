---
layout: post
title: "Sending Android Logs with Live Templates in IntelliJ IDEA"
date: xxxx-xx-xx xx:xx
comments: true
categories: Logs IntelliJ-IDEA Live-Templates
---

#Sending Android Logs with Live Templates in IntelliJ IDEA 

As professional developers we are constantly looking  for productive tips to save our precious time. IntelliJ IDEA comes with many tricks and shortcuts. One of them are Live Templates.

Live templates are predefined code fragments, which allow us to code faster and finally to become more productive. We can create our custom live templates and edit existing.


They are stored in the following location:

* Windows:* 

	`<your home directory>\.<product name><version number>\config\templates* `

* Linux:

	`~\.<product name><version number>\config\templates* `

* MacOS:

	`~/Library/Preferences/<product name><version number>/templates`

To create new live templates we need to launch Settings and open Live Templates page. Then we can select template group or create new one. I chose second option and called my template group: “Android”.


Next add new template and fill fields as below. I decided for following naming convention: first letter – “l” (from Log), second letter – “d,e,i,w” (from Log type), third letter if exists – “t” (from TAG, if it is defined).

* Abbreviation: ld
* Desription: Sends DEBUG log message
* Template text:

	```
Log.d("$TAG$", "$MSG$");
	
	```
	
* Applicable in Java: statement



I also added another template for logs with defined TAG as static (abbreviation with “t” suffix, e.g. ldt, let):

* Abbreviation: ldt
* Desription: Sends DEBUG log message
* Template text:

	```
Log.d(TAG, "$MSG$");

	```

* Abbreviation: le
* Desription: Sends ERROR log message
* Template text:

	```
Log.e("$TAG$", "$MSG$");

	```

* Abbreviation: let
* Applicable in Java: statement

Finally it should look as below:

![Zrzut ekranu 2014-05-21 o 22.14.08](http://awcntt-article-image.qiniudn.com/issue103_Zrzut-ekranu-2014-05-21-o-21.52.21-300x192.png)

In code it works perfectly:

![Zrzut ekranu 2014-05-21 o 22.14.08](http://awcntt-article-image.qiniudn.com/issue103_Zrzut-ekranu-2014-05-21-o-22.14.08.png)

![Zrzut ekranu 2014-05-21 o 22.16.10](http://awcntt-article-image.qiniudn.com/issue103_Zrzut-ekranu-2014-05-21-o-22.16.10.png)

![Zrzut ekranu 2014-05-21 o 22.16.49](http://www.pawegio.com/wp-content/uploads/2014/05/Zrzut-ekranu-2014-05-21-o-22.16.49.png)

![Zrzut ekranu 2014-05-21 o 22.17.01](http://awcntt-article-image.qiniudn.com/issue103_Zrzut-ekranu-2014-05-21-o-22.17.01.png)

I am also attaching Android.xml file from Live Templates directory so you can faster add templates I created above. I was a bit lazy, but you can add relevant templates for other log types (e.g. info, verbose, warn).

	<?xml version="1.0" encoding="UTF-8"?>
	<templateSet group="Android">
  		<template name="ld" value="Log.d($TAG$, $MSG$);" description="Sends DEBUG log message" 	toReformat="false" toShortenFQNames="true">
    		<variable name="TAG" expression="" defaultValue="" alwaysStopAt="true" />
    		<variable name="MSG" expression="" defaultValue="" alwaysStopAt="true" />
    		<context>
      			<option name="JAVA_CODE" value="false" />
      			<option name="JAVA_STATEMENT" value="true" />
      			<option name="JAVA_EXPRESSION" value="false" />
      			<option name="JAVA_DECLARATION" value="false" />
      			<option name="JAVA_COMMENT" value="false" />
      			<option name="JAVA_STRING" value="false" />
      			<option name="COMPLETION" value="false" />
    		</context>
  		</template>
  		<template name="ldt" value="Log.d(TAG, $MSG$);" description="Sends DEBUG log message with TAG" toReformat="false" toShortenFQNames="true">
    	<variable name="MSG" expression="" defaultValue="" alwaysStopAt="true" />
    		<context>
      			<option name="JAVA_CODE" value="false" />
      			<option name="JAVA_STATEMENT" value="true" />
      			<option name="JAVA_EXPRESSION" value="false" />
     	 		<option name="JAVA_DECLARATION" value="false" />
      			<option name="JAVA_COMMENT" value="false" />
      			<option name="JAVA_STRING" value="false" />
      			<option name="COMPLETION" value="false" />
    		</context>
  		</template>
  		<template name="le" value="Log.e($TAG$, $MSG$);" description="Sends ERROR log message" toReformat="false" toShortenFQNames="true">
    		<variable name="TAG" expression="" defaultValue="" alwaysStopAt="true" />
   			<variable name="MSG" expression="" defaultValue="" alwaysStopAt="true" />
    		<context>
      			<option name="JAVA_CODE" value="false" />
      			<option name="JAVA_STATEMENT" value="true" />
      			<option name="JAVA_EXPRESSION" value="false" />
      			<option name="JAVA_DECLARATION" value="false" />
      			<option name="JAVA_COMMENT" value="false" />
      			<option name="JAVA_STRING" value="false" />
      			<option name="COMPLETION" value="false" />
    		</context>
  		</template>
  		<template name="let" value="Log.e(TAG, $MSG$);" description="Sends ERROR log message with TAG" toReformat="false" toShortenFQNames="true">
    		<variable name="MSG" expression="" defaultValue="" alwaysStopAt="true" />
    		<context>
      			<option name="JAVA_CODE" value="false" />
      			<option name="JAVA_STATEMENT" value="true" />
      			<option name="JAVA_EXPRESSION" value="false" />
      			<option name="JAVA_DECLARATION" value="false" />
      			<option name="JAVA_COMMENT" value="false" />
      			<option name="JAVA_STRING" value="false" />
      			<option name="COMPLETION" value="false" />
    		</context>
  		</template>
</templateSet>

More about Live Templates you can find [here](http://www.jetbrains.com/idea/webhelp/live-templates.html).

---


原文地址：[http://www.pawegio.com/2014/05/20/sending-android-logs-with-live-templates-in-intellij-idea-and-android-studio/](http://www.pawegio.com/2014/05/20/sending-android-logs-with-live-templates-in-intellij-idea-and-android-studio/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)