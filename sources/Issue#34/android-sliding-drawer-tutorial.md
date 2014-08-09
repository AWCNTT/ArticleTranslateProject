---
layout: post
title: "Android Sliding Drawer Tutorial"
date: 2014-08-09 17:23
comments: true
categories: SlidingDrawer
---

#Android Sliding Drawer Tutorial

Hello [Android developers](http://www.androidpeople.com/android-tutorial/android-application-developer/),

In this post we will how to implement the Sliding Drawer widget in our android application.

What is sliding widget?

Sliding widget is a object container that has the ability to slide and occupy the full screen when ever there are triggered.

When this will be useful?

Sliding widget is much used in application that gives much preference to animation, Consider the notifcation and also the general application container in default android OS are Sliding widget.

We can use them to show notification in our application to intimate an even in our application or use them instead of dialog box having the click buttons in the sliding drawer.

There are two main important component

1. **Sliding Handler** - This is the one contains handler button that is always visible in the application.
2. **Sliding Container** - The object container that animate slide up/down to show the objects.


We hardly use coding in the tutorial most of the part are designed in XML layout itself.

**Adjust sliding drawer height** :

To adjust the sliding drawer size we can specify size in layout height in dip eg: 200 dip.

**Adding image to sliding drawer handler** :

We can set any resource image as background in button properties.

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout android:id="@+id/LinearLayout01"
	android:layout_width="fill_parent" 
	android:layout_height="fill_parent"
	xmlns:android="http://schemas.android.com/apk/res/android"
	android:orientation="vertical"
	android:gravity="bottom"
	android:background="@drawable/androidpeople">
	
	<SlidingDrawer 
		android:layout_width="wrap_content" 
		android:id="@+id/SlidingDrawer" 		
		android:handle="@+id/slideHandleButton"
		android:content="@+id/contentLayout"
		android:padding="10dip"
		android:layout_height="250dip">
		
		<Button 
			android:layout_width="wrap_content" 
			android:layout_height="wrap_content"
	 		android:id="@+id/slideHandleButton"
  			android:background="@drawable/closearrow">
  		</Button>
  		
		<LinearLayout 
			android:layout_width="wrap_content"
	 		android:id="@+id/contentLayout"
  			android:orientation="vertical"
  			android:gravity="center|top"
 			android:padding="10dip"
			android:background="#C0C0C0"
			android:layout_height="wrap_content">
			
			<Button android:id="@+id/Button01" 
					android:layout_width="wrap_content" 
					android:layout_height="wrap_content"
					android:text="Content">
			</Button>
			<Button android:id="@+id/Button02"
		 			android:layout_width="wrap_content" 
		 			android:layout_height="wrap_content"
	 				android:text="Content">
	 		</Button>
			<Button android:id="@+id/Button03"
		 			android:layout_width="wrap_content" 
		 			android:layout_height="wrap_content"
	 				android:text="Content">
	 		</Button>
		</LinearLayout>
	</SlidingDrawer>

</LinearLayout>
```

The java code here is use to toggle the handler button image between up and down.

```
import android.app.Activity;
import android.os.Bundle;
import android.widget.Button;
import android.widget.SlidingDrawer;
import android.widget.SlidingDrawer.OnDrawerCloseListener;
import android.widget.SlidingDrawer.OnDrawerOpenListener;

public class slidingDrawerExample extends Activity {

Button slideHandleButton;
SlidingDrawer slidingDrawer;

public void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);

setContentView(R.layout.main);
slideHandleButton = (Button) findViewById(R.id.slideHandleButton);
slidingDrawer = (SlidingDrawer) findViewById(R.id.SlidingDrawer);

slidingDrawer.setOnDrawerOpenListener(new OnDrawerOpenListener() {

@Override
public void onDrawerOpened() {
slideHandleButton.setBackgroundResource(R.drawable.openarrow);
}
});

slidingDrawer.setOnDrawerCloseListener(new OnDrawerCloseListener() {

@Override
public void onDrawerClosed() {
slideHandleButton.setBackgroundResource(R.drawable.closearrow);
}
});

}

}
```

![Screen Shots](http://www.androidpeople.com/wp-content/uploads/2010/07/slidingDrawer-androidpeople.png)

![slidingDrawer](http://www.androidpeople.com/wp-content/uploads/2010/07/slidingDrawerUp-androidpeople.png)

[Download Source code here](http://www.androidpeople.com/wp-content/uploads/2010/07/SlidingDrawer.zip).

---


原文地址：[https://github.com/AWCNTT/ArticleTranslateProject](https://github.com/AWCNTT/ArticleTranslateProject)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)