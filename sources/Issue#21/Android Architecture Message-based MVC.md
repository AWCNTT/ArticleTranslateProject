---
layout: post
title: "文章中文标题xxxx"
date: xxxx-xx-xx xx:xx
comments: true
categories: xxxxx(文章关键字，多个关键字用英文空格分割)
---

How do you separate application state, user interaction logic and data presentation in your Android apps?

![image](http://mindtherobot.com/blog/wp-content/uploads/2010/11/asyncmvc1.jpg)

Platform designers did not enforce any high-level application architecture framework upon us but left us enough options to implement our own solutions based on application requirements and scale. Most simple applications will get away with just storing data in their widgets (such as in text fields, spinners etc.) and their state manipulation right in event handlers such as OnClickListener‘s. However, if you are going to write a complex application or plan to sophisticate your simple app further, you should really think how to layer it well so that the architecture supports adding new features and satisfies the expected performance, flexibility, responsiveness and other requirements, and your code does not become a mess.

In this article, I will show you one practical approach to dividing application code into three layers according the MVC paradigm and connecting the view to the controller using the Android messaging framework. I used it in my own code and although it might not be 100% academically correct or applicable for every possible app, I love the benefits it gives to me as my app grows more and more complex.

##Intro

I will not go deep into explaining the MVC architecture pattern – there’s a lot of places where you can read about it. I wrote [an article on how to apply this architecture to a single component](http://www.wiseandroid.com/post/2010/07/19/Use-MVC-and-develop-a-simple-Star-Rating-widget-on-Android.aspx) but it can also be applied to the entire application. I expect you understand what the Model, View and Controller layers are expected to be there for.

In addition, if you did not read [the article on Handler](http://mindtherobot.com/blog/159/android-guts-intro-to-loopers-and-handlers/), please do so. Although it does not cover messaging per se, it gives you understanding of the threading model that is behind it.

I wrote a sample app that I will use throughout this article. You can download the complete source at the end of the article.

##Part 1: Model

It is a good sign when the Model layer is generic and is only tied to the domain logic. In our demo app, it is completely obvious and boring, except for few things.

First, there is a separate class that contains the model “data”, in other words, whatever state that might change in the Model layer during the application lifecycle:

```java
@Immutable
public final class ModelData implements Serializable {
 
  private static final long serialVersionUID = 1L;
 
  private final int answer;
   
  public ModelData(int answer) {
    this.answer = answer;
  }
   
  public final int getAnswer() {
    return answer;
  }
}
```

As you can see, I like to put a descriptive annotation like **@Immutable** so that I remember this class is actually immutable which gives a lot of benefits such as the ability to share instances between threads and others. In addition, I made the class **Serializable** – serializing it would allow to, for example, implement a “Save/Load” feature.

As for the Model class itself, here it is:

```java
@ThreadSafe
public class Model {
  public interface Listener {
    void onModelStateUpdated(Model model);
  }
   
  private ModelData data = new ModelData(0);
   
  private final List<Listener> listeners = new ArrayList<Listener>();
   
  public Model() {
     
  }
   
  public final ModelData getData() {
    synchronized (this) {
      return data;
    }
  }
   
  public final void updateData() { // takes a while!
    SystemClock.sleep(5000);
    ModelData newData = new ModelData(new Random().nextInt(10) + 1);
     
    synchronized (this) {
      data = newData;
    }
     
    synchronized (listeners) {
      for (Listener listener : listeners) {
        listener.onModelStateUpdated(this);
      }
    }
  }
   
  public final void addListener(Listener listener) {
    synchronized (listeners) {
      listeners.add(listener);
    }
  }
   
  public final void removeListener(Listener listener) {
    synchronized (listeners) {
      listeners.remove(listener);
    }
  }
}
```

As you can see, the **updateData()** method is written so that it takes some time to execute, which in the real life could be a request to the server and/or a heavy computation. The data is kept protected from concurrent access using **synchronized**. Listeners can also be added and removed from concurrent threads. If you actually understood MVC, you should realize that model listeners are a core part of the pattern – they allow you to tie any code to the model without the model knowing anything about it (and thus the model code avoids any non-business logic injection).

Now to the most interesting part – the Controller layer.

##Part 2: Controller

At this point you can implement the Controller layer in various ways. The way I chose to present is based on **Handler**, **HandlerThread** and **Message** classes. Again, you should have a basic understanding of how they work.

In essence, with those classes the Android platform allows you to implement effective, safe and powerful messaging between various pieces of your app. In our case, what I’d like to do is:

 - Give the Controller an **inbox** handler – so that the View layer can send messages to it, such as “the user asked us to quit” or “the user asked us to update the data”.
 - Give the ability to attach one or multiple **outbox** handlers to the Controller, so that the Controller can send its own messages to the View (or Views) – such as “model update finished” or “destroy yourself, we are quitting” etc.
 - This way, the View can only turn user events into messages and send them to Controller without thinking of how to react to them, and whenever the Controller gives any message to the View, the View will mindlessly execute whatever the Controller tells it while presenting the application status to the user. Thus we get a clean implementation of the MVC paradigm.

Now, since every **Handler** needs a **Looper**, we need to choose a thread where we will run the Controller’s inbox handler. We could either do it on the UI thread (and it will process both UI messages and Controller’s messages) or on a new separate **HandlerThread**. Normally our Controller is going to process its messages without blocking on slow operations so that it can stay responsive to the View. Any slow operations will be run in background threads. Thus, we could use the UI thread for Controller’s messages without worrying much. However, I chose to run a separate thread just because I think that is a cleaner architecture and perhaps it could be a bit more responsive. Since the messaging framework saves us from many concurrency headaches, we can easily jump from one solution to the other without changing much code.

This is how we manage the inbox and outbox handlers in the code:

```java
public class Controller {
 
        // ... some code omitted ...
 
  private final HandlerThread inboxHandlerThread;
  private final Handler inboxHandler;
  private final List<Handler> outboxHandlers = new ArrayList<Handler>();
   
        // ... some code omitted ...
 
  public Controller(Model model) {
    this.model = model;
     
    inboxHandlerThread = new HandlerThread("Controller Inbox"); // note you can also set a priority here
    inboxHandlerThread.start();
      
        // ... some code omitted ...
 
    inboxHandler = new Handler(inboxHandlerThread.getLooper()) {
      @Override
      public void handleMessage(Message msg) {
        Controller.this.handleMessage(msg);
      }
    };
  }
   
  public final void dispose() {
    // ask the inbox thread to exit gracefully
    inboxHandlerThread.getLooper().quit();
  }
   
  public final Handler getInboxHandler() {
    return inboxHandler;
  }
   
  public final void addOutboxHandler(Handler handler) {
    outboxHandlers.add(handler);
  }
 
  public final void removeOutboxHandler(Handler handler) {
    outboxHandlers.remove(handler);
  }
   
  final void notifyOutboxHandlers(int what, int arg1, int arg2, Object obj) {
    if (outboxHandlers.isEmpty()) {
      Log.w(TAG, String.format("No outbox handler to handle outgoing message (%d)", what));
    } else {
      for (Handler handler : outboxHandlers) {
        Message msg = Message.obtain(handler, what, arg1, arg2, obj);
        msg.sendToTarget();
      }
    }
  }
 
        // ... some code omitted ...
}
```

This is not the complete code for Controller, we’ll come back to it later. OK, so what you can see is how we initialize and bring down the inbox handler and its thread, how you can subscribe your handler as an outbox one and you how you notify all outbox handlers with a message.

With this code, we can take the inbox handler and send message to it from the View layer and at the same time have a View-bound handler registered as an outbox handler for the Controller, and that will allow the Controller and the View to exchange async messages effectively.

Now in order to actually handle incoming messages and do something, we need to implement the **handleMessage()** method in the **Controller** class. In most applications you will want to implement an instance of the **State design pattern** here because depending on what the application is doing right now it might handle the same messages differently, or ignore some of them. For example, in our application, once you start refreshing the Model, we wouldn’t like the user to allow refreshing it again until we’re done. To illustrate that, here’s the **ControllerState** interface and its **ReadyState** implementation:

```java
public interface ControllerState {
  boolean handleMessage(Message msg);
}
 
final class ReadyState implements ControllerState {
 
  private final Controller controller;
   
  public ReadyState(Controller controller) {
    this.controller = controller;
  }
   
  @Override
  public final boolean handleMessage(Message msg) {
    switch (msg.what) {
    case V_REQUEST_QUIT:
      onRequestQuit();
      return true;
    case V_REQUEST_UPDATE:
      onRequestUpdate();
      return true;
    case V_REQUEST_DATA:
      onRequestData();
      return true;
    }
    return false;
  }
 
  private void onRequestData() {
    // send the data to the outbox handlers (view)
    controller.notifyOutboxHandlers(C_DATA, 0, 0, controller.getModel().getData());
  }
 
  private void onRequestUpdate() {
    // we can't just call model.updateState() here because it will block
    // the inbox thread where this processing is happening.
    // thus we change the state to UpdatingState that will launch and manage
    // a background thread that will do that operation
 
    controller.changeState(new UpdatingState(controller));
  }
 
  private void onRequestQuit() {
    controller.quit();
  }
}
```

By putting the Controller and its states to the same package, we can give them access to Controller’s internals using the default access level. I won’t put **UpdatingState** code here, it’s in the package at the bottom of the page.

Now here’s how Controller delegates message processing to its state:

```java
public class Controller {
 
        // ... some code omitted ...
  private ControllerState state;
   
  public Controller(Model model) {
    this.model = model;
     
        // ... some code omitted ...
      
    this.state = new ReadyState(this);
 
        // ... some code omitted ...
  }
   
        // ... some code omitted ...
 
  private void handleMessage(Message msg) {
    Log.d(TAG, "Received message: " + msg);
     
    if (! state.handleMessage(msg)) {
      Log.w(TAG, "Unknown message: " + msg);
    }
  }
   
  final Model getModel() {
    return model;
  }
 
  final void quit() {
    notifyOutboxHandlers(C_QUIT, 0, 0, null);
  }
   
  final void changeState(ControllerState newState) {
    Log.d(TAG, String.format("Changing state from %s to %s", state, newState));
    state = newState;
  }
}
```

##Part 3: View

Without many explanations – I guess you’ve got the idea already – here’s the View code (**DemoActivity**):

```java
public class DemoActivity extends Activity implements Handler.Callback, OnClickListener {
   
  private static final String TAG = DemoActivity.class.getSimpleName();
   
  private Controller controller;
   
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
 
        setContentView(R.layout.main);
         
        ((Button) findViewById(R.id.update)).setOnClickListener(this);
        ((Button) findViewById(R.id.quit)).setOnClickListener(this);
         
        controller = new Controller(new Model());
        controller.addOutboxHandler(new Handler(this)); // messages will go to .handleMessage()
         
        controller.getInboxHandler().sendEmptyMessage(V_REQUEST_DATA); // request initial data
    }
 
  @Override
  protected void onDestroy() {
    // I think it is a good idea to not fail in onDestroy()
    try {
      controller.dispose();
    } catch (Throwable t) {
      Log.e(TAG, "Failed to destroy the controller", t);
    } 
 
    super.onDestroy();
  }
 
  @Override
  public boolean handleMessage(Message msg) {
    Log.d(TAG, "Received message: " + msg);
      
    switch (msg.what) {
    case C_QUIT:
      onQuit();
      return true;
    case C_DATA:
      onData((ModelData) msg.obj);
      return true;
    case C_UPDATE_STARTED:
      onUpdateStarted();
      return true;
    case C_UPDATE_FINISHED:
      onUpdateFinished();
      return true;
    }
    return false;
  }
 
  private void onUpdateStarted() {
    ProgressBar progressBar = (ProgressBar) findViewById(R.id.progress_bar);
    progressBar.setVisibility(View.VISIBLE);
  }
 
  private void onUpdateFinished() {
    ProgressBar progressBar = (ProgressBar) findViewById(R.id.progress_bar);
    progressBar.setVisibility(View.GONE);
     
    // request the updated data
    controller.getInboxHandler().sendEmptyMessage(V_REQUEST_DATA);
  }
 
  private void onData(ModelData data) {
    TextView dataView = (TextView) findViewById(R.id.data_view);
    dataView.setText("The answer is "+ data.getAnswer());
  }
 
  private void onQuit() {
    Log.d(TAG, "Activity quitting");
    finish();
  }
 
  @Override
  public void onClick(View v) {
    switch (v.getId()) {
    case R.id.update:
      controller.getInboxHandler().sendEmptyMessage(V_REQUEST_UPDATE);
      break;
    case R.id.quit:
      controller.getInboxHandler().sendEmptyMessage(V_REQUEST_QUIT);
      break;
    }
  }
}
```

In this demo app, the Activity actually owns the **Controller** instance which holds the model. However, you might consider moving the Controller to a Service if its lifecycle fits the Controller/Model lifecycle better.

##Summary

As you can see, the message-based approach really allows building a strong complex Controller and a completely dumb View. That allows us to achieve a good MVC implementation.

As I wrote it in the very beginning, this solution might not be completely valid from the theoretical point of view or not good for any app, however I have experience building a very complex app with many states and it is completely based on messages. There are many weaknesses in this architecture, such as the need to collect message codes somewhere (see **ControllerProtocol**) and to define message parameters in documentation. But the upside is very good layer separation which gives you enormous benefits as the app grows.

##Attachment: [async-mvc.zip](http://mindtherobot.com/blog/wp-content/uploads/2010/11/async-mvc.zip)



---


原文地址：[https://github.com/AWCNTT/ArticleTranslateProject](https://github.com/AWCNTT/ArticleTranslateProject)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)