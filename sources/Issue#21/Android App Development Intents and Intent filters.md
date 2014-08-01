---
layout: post
title: "文章中文标题xxxx"
date: xxxx-xx-xx xx:xx
comments: true
categories: xxxxx(文章关键字，多个关键字用英文空格分割)
---

Intents are used by an application to interact with the phone’s hardware components or other applications, or to start a service or activity with a certain piece of data or to broadcast that an event has occurred.

##Using Intents to launch phone activities:

We can use Intents to launch the phone’s basic activities such as the phone dialer, the browser or search.

These intents are called **Implicit Intents** cause you don’t specify the activity you want to launch, rather Android determines the proper activity to launch based on the required action. Also when the launched activity finisheds its work, the original activity has no information that the launched activity has finished it’s work

In this example we create an intent that performs a phone number dial action. We don not specify that we want the dialer activity to launch, rather we specify that we want to dial a number and Android launches the dialer activity to perform this action

Consider this activity: consists of a TextView and a button to dial the number in the text view.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    >
  <TextView
    android:layout_width="fill_parent"
    android:layout_height="wrap_content"
    android:text="Enter the phone number"
    />
  <EditText
  android:layout_width="fill_parent"
  android:layout_height="wrap_content"
  android:id="@+id/txtNumber"
  android:inputType="phone"
  />
  <Button
  android:layout_width="wrap_content"
  android:layout_height="wrap_content"
  android:text="Dial"
  android:id="@+id/btnDial"
  />
</LinearLayout>
```

![image1](http://mobileorchard.com/wp-content/uploads/2011/02/intents1-200x300.jpg)

When you press the button the phone dialer launches and then you can call the number.
This is done using the following code:

```java
btnDial.setOnClickListener(new OnClickListener() {

   public void onClick(View v) {
    // TODO Auto-generated method stub

      Intent dialIntent=new Intent(Intent.ACTION_DIAL,Uri.parse("tel:"+(txtNumber.getText()).toString()));

      startActivity(dialIntent);
   }
  });
```

Notice that the dialer has been launched but the user has to press the call button to make a call.

![image2](http://mobileorchard.com/wp-content/uploads/2011/02/intents2-209x300.jpg)

If you want the phone to dial the number automatically you could have used this intent:Intent.ACTION_CALL

If you use this it requires adding the following permission to the manifest file:<uses-permission android:name="android.permission.CALL_PHONE">

Another example to launch an intent to open the browser and navigate to a certain URL:
```java
Intent in=new Intent(Intent.ACTION_VIEW, Uri.parse("http://mobileorchard.com"));
startActivity(in);
```
For a list of available phone actions, check this [link](http://developer.android.com/reference/android/content/Intent.html)

##Intent Filters:

Android components like activities can also serve implicit intents. but to do so they have to filter all implicit intents in order to serve only the intents they desire to serve. this is done using intent filters.

Suppose you want  to create an activity that acts as the default dialer activity. You must associate an intent filter with this activity in order to that this activity serve the dial intents only.

Let’s demonstrate a simple example which is creating an application with one activity that we want to make it a dialer activity.

Create a new android project, create an activity and name it **Dialer**.

In the AndroidManifest.xml file of this application add the following to the Dialer activity:

```xml
<intent-filter android:priority="100" >
            <action android:name="android.intent.action.DIAL"  />
                <category android:name="android.intent.category.DEFAULT" />
                <data android:scheme="tel"/>
            </intent-filter>
```
to become:
```xml
<activity android:name=".Dialer"
                  android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
            <intent-filter android:priority="100" >
            <action android:name="android.intent.action.DIAL"  />
                <category android:name="android.intent.category.DEFAULT" />
                <data android:scheme="tel"/>
            </intent-filter>
        </activity>
```

Now what we have done is adding an intent filter to that activity. this intent filter has the following properties:

 - Action: the type of implicit intents that this activity responds to. in our case it is the dial action. higher numbers represent higher priority.
 - Priority: about the priority of that activity over other activities that respond to the same type of intents.
 - Category: Implicit intents have built-in categories. in order that an implicit intent be captured by our activity, the implicit intent category must match our activity category.
 - Data: adds data specification scheme to the intent filter.

So if any other application has the following module to launch the dialer:
```java
Intent in=new Intent(Intent.ACTION_DIAL, Uri.parse("tel:000"));
      startActivity(in);
```


The user will see the following dialog offering him/her the choice between the default dialer and our custom dialer.

![image3](http://mobileorchard.com/wp-content/uploads/2011/02/Intents3-300x256.png)

That is is for implicit intents and intent filters. In the next post we’re going to explore another type of intents which is explicit intents.


---


原文地址：[https://github.com/AWCNTT/ArticleTranslateProject](https://github.com/AWCNTT/ArticleTranslateProject)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)