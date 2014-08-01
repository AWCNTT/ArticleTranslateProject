---
layout: post
title: "文章中文标题xxxx"
date: xxxx-xx-xx xx:xx
comments: true
categories: xxxxx(文章关键字，多个关键字用英文空格分割)
---

One of the reasons I love Android API is because it contains so many useful little things. Many of them are not even specific to the platform and I really miss them in Java SE. Today I’d like to give a short introduction to two nice classes – **Looper** and **Handler**. They are used in Android UI internally, and available to us developers so we can do some cool things with their help.

So what can you do with **Loopers** and **handlers**? Basically, they implement a common concurrency pattern that I call the **Pipeline Thread**. Here’s how it works:her requirements, and your code does not become a mess.

 - The Pipeline Thread holds **a queue of tasks** which are just some units of work that can be executed or processed.
 - Other threads can safely push new tasks into the Pipeline Thread’s queue at any time.
 - The Pipeline Thread processes the queued tasks **one after another**. If there are no tasks queued, it blocks until a task appears in the queue.
 - Sometimes tasks can called **messages** and other names.

This architecture has some valuable traits and is widely used in frameworks and applications on different platforms.

In this post, we are going to build **a simple app that emulates a queue of background downloads** while showing its status in the UI. It will be based on a Pipeline Thread that we will build using **Looper** and **Handler**. As usual, the complete source is available at the bottom of the article.

But before we start, let’s discuss the Pipeline Thread for some more time and see how **Looper** and **Handler** implement it.

##Usages of the Pipeline Thread

The most prominent usage of Pipeline Thread is found within **almost any UI framework**, including Swing (remember the event-dispatching thread?), SWT, Adobe Flex and, of course, Android Activities. The Pipeline Thread pattern **is used to process UI events** (such as button clicks, mouse movement, orientation changes, screen redraw and so on). This allows you to change a button caption without having to worry that the user will click the button at the same moment.

On the other hand, this forces you to only do quick operations in the UI thread – any developer knows what happens if you try to download a file right there in a button onClick handler. In our app we will overcome that by adding another Pipeline Thread and pushing long-lasting operations (in our case, the downloads) to it so that the UI thread can run free.

Other common usages of the Pipeline Thread pattern:
 - Executing **requests** to a remote service (usually you want to do them one by one and sequentially)
 - **Uploading images** to an HTTP service
 - Resizing and processing images (I once developed a picture uploader :) )
 - Downloading stuff, just like we’re going to do in our app

In general, using a Pipeline Thread rather than launching another thread for each background operation allows you to control the load and order (almost like law and order) of your background jobs. Also, you can have multiple Pipeline Threads and use them as a pool so you will have both the ability to execute multiple operations at once and keep the load under control.

In our example, we only want to allow one download at a time and we want downloads to execute in the order they were scheduled. Exactly the case to use a Pipeline Thread.

##Looping and Handling

[Looper](http://developer.android.com/reference/android/os/Looper.html) is a class that turns a thread into a Pipeline Thread and Handler gives you a mechanism to push tasks into it from any other threads.

The **Looper** is named so because it implements the loop – takes the next task, executes it, then takes the next one and so on. The **Handler** is called a handler because someone could not invent a better name.

Here’s what you should put into a [Thread‘s](http://developer.android.com/reference/java/lang/Thread.html) **run()** method to turn it into a Pipeline Thread and to create a **Handler** so that other threads can assign tasks to it:

```java
@Override
public void run() {
  try {
    // preparing a looper on current thread     
    // the current thread is being detected implicitly
    Looper.prepare();
 
    // now, the handler will automatically bind to the
    // Looper that is attached to the current thread
    // You don't need to specify the Looper explicitly
    handler = new Handler();
     
    // After the following line the thread will start
    // running the message loop and will not normally
    // exit the loop unless a problem happens or you
    // quit() the looper (see below)
    Looper.loop();
  } catch (Throwable t) {
    Log.e(TAG, "halted due to an error", t);
  } 
}
```

After that, you can just pass the handler to any other thread. It has a thread-safe interface that includes many operations, but the most straightforward ones are **postMessage()** and its relatives.

**Note**: The **Handler** interface has many more cool operations, especially related to message passing, that I will not cover in this example.

For example, imagine another thread has a reference to the **handler** that was created in our Pipeline Thread. Here’s how that other thread can schedule an operation to be executed in the Pipeline Thread:

```java
handler.post(new Runnable() {
  @Override
  public void run() {       
    // this will be done in the Pipeline Thread       
  }
});
```

In our case, we will use this idiom to schedule download tasks to be performed (on the download pipeline thread) when the user clicks a button (which is handled on the UI thread). We will also use it the other way around – when the download thread notifies the activity that a download has completed (on the download thread), the activity will use a **Handler** that is attached to the UI thread to make sure that we update the UI on the UI thread only (which is a requirement of Android UI).

By the way, the UI thread has a **Looper** created for it implicitly, so you can just create a Handler in activity’s **onCreate()** and it will work fine:

```java
@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);
     
    // Create the Handler. It will implicitly bind to the Looper
    // that is internally created for this thread (since it is the UI thread)
    handler = new Handler();
}
```

##Implementation Details

If you understood the idea of **Looper** and **Handler**, the rest is details. Basically, I created a **DownloadTask** class that emulates a download task of a random duration. I did not want to do actual downloads because I didn’t want to eat your data plans, but it’s straightforward to change this into real work. Then, the **DownloadThread** has an interface that allows to enqueue **DownloadTask** instances and also to request it to exit gracefully. There is also the **DownloadThreadListener** interface that allows the download thread to notify some other entity about status updates. In our case, that interface will be implemented by the **DownloadQueueActivity** because we want to reflect the download progress in the UI.

At this point, however, we need to use another **Handler** – the one that is attached to the UI thread. The **DownloadThreadListener** is notified by the **DownloadThread** from the download thread itself, but the action we want to take (updating the progressbar and the textview) needs to be done from the UI thread. Therefore we use the UI handler like this:

```java
// note! this might be called from another thread
@Override
public void handleDownloadThreadUpdate() {
  // we want to modify the progress bar so we need to do it from the UI thread 
  // how can we make sure the code runs in the UI thread? use the handler!
  handler.post(new Runnable() {
    @Override
    public void run() {
      // update the UI etc.
    }
  });
}
```

**Note**: In case of doubt, it’s also OK to call a handler from its own thread.

Of course, you should read the code and the comments to understand things clearly.

Also, it’s better to do things like background downloads in a **service**, as described in my previous article.

##Conclusion

**Loopers** and **Handlers** allow you to do cool things. However, they are related to concurrency which is often a slippery area. Be sure to ask your questions and give feedback in the comments – and to stay tuned for updates!

##Attachment: [the complete project source](http://mindtherobot.com/blog/wp-content/uploads/2010/06/downloadqueue.zip)


---


原文地址：[https://github.com/AWCNTT/ArticleTranslateProject](https://github.com/AWCNTT/ArticleTranslateProject)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)