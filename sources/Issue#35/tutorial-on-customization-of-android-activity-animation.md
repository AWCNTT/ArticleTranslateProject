---
layout: post
title: "Tutorial on customization of Android Activity Animation"
date: 2014-08-15 17:48
comments: true
categories: Activity Animation
---

# Tutorial on customization of Android Activity Animation

If you are thinking on customizing the animation of Activity transition then probably you would look for ActivityOptions class introduced in Android 4.1 (Jelly bean). This class has provided three methods which can help you to customize the Activity Animation. These are given in the table below.

### ActivityOptions Class Methods

<table class="c4rtablestyle">
<tbody><tr>
<th>Function Name</th>
<th>Description</th>
</tr>
</tbody><tbody>
<tr>
<td>makeCustomAnimation</td>
<td>This method allows to pass custom animation and when the Atyctivi is launched, it gets rendered&nbsp;accordingly. Here you can pass animation for transitioning out Activity as well as for transitioning in Activity</td>
</tr>
<tr>
<td>makeScaleUpAnimation</td>
<td>This method scales up the Activity from the initial size to its final representational size. It can be used to scale up the activity from the view which has&nbsp;launched&nbsp;this activity.</td>
</tr>
<tr>
<td>makeThumbnailScaleUpAnimation</td>
<td>In this animation, a thumbnail of the activity scales up to the final size of the activity.</td>
</tr>
<tr>
<td>toBundle</td>
<td>This method returns Bundle object which can be passed in the startActivity() method for desired animation.</td>
</tr>
</tbody>
</table>

Function Name	Description
makeCustomAnimation	This method allows to pass custom animation and when the Atyctivi is launched, it gets rendered accordingly. Here you can pass animation for transitioning out Activity as well as for transitioning in Activity
makeScaleUpAnimation	This method scales up the Activity from the initial size to its final representational size. It can be used to scale up the activity from the view which has launched this activity.
makeThumbnailScaleUpAnimation	In this animation, a thumbnail of the activity scales up to the final size of the activity.
toBundle	This method returns Bundle object which can be passed in the startActivity() method for desired animation.
For more information on ActivityOptions you can refer [here]([http://developer.android.com/reference/android/app/ActivityOptions.html).

* **Project Information**: Meta-data about the project.
* **Platform Version** : Android API Level 16.
* **IDE** : Eclipse Helios Service Release 2
* **Emulator** : Android 4.1(API 16)


**Prerequisite**: Preliminary knowledge of Android application framework, and Intent.



### Sample Source Code:

We create a project using eclipse and then create anim (Animation) folder under res(resource) folder. Now we will define the animation attributes in the xml files and put it in anim folder. Here, we are going to define two animations which will be used in makeCustomAnimation() method. makeCustomAnimation takes two animation files, one for incoming activity and another for outgoing activity. Either of the animations can be null and in that case animation will not be performed for that particular activity. Now we will define fade_in.xml for incoming activity. Here we are going to change the Alpha value from 0 to 1 which makes activity transparent to opaque.

```
<alpha xmlns:android="http://schemas.android.com/apk/res/android"
   android:interpolator="@android:anim/anticipate_interpolator"
   android:fromAlpha="0.0" android:toAlpha="1.0"
   android:duration="@android:integer/config_longAnimTime" />
```

Now we are going to define the another file called fade_out.xml file for transitioning out Activity. Here we will change the value of Alpha from 1 to 0.

```
<alpha xmlns:android="http://schemas.android.com/apk/res/android"
   android:interpolator="@android:anim/anticipate_interpolator"
   android:fromAlpha="1.0" android:toAlpha="0.0"
   android:duration="@android:integer/config_longAnimTime" />
```

Now we are going to define the layout file for the main activity. Name this file as acitivity_main.xml. In this file we will add three buttons for corresponding animation.

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >
 
    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="fadeAnimation"
        android:text="@string/btFadeAnimation" />
    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="scaleupAnimation"
        android:text="@string/btScaleupAni" />
   <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:onClick="thumbNailScaleAnimation"
        android:text="@string/btThumbNailScaleupAni" />
 
</LinearLayout>
```
As you may have noticed that we have already attached onclick method with each button. These methods will animate the activity when it is launched using startActivity() method. Now let’s define the another layout for the target Activity with one ImageView. Put an image in drawable folder and then use that image as src for Image view. Here I have put “freelance2.jpg” image in drawable folder and have used android:src tag to use the image. Name the layout file as activity_animation.xml

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:layout_width="match_parent"
   android:layout_height="match_parent"
   android:orientation="vertical" >
   <ImageView
     android:id="@+id/imageView1"
     android:layout_width="match_parent"
     android:layout_height="match_parent"
     android:layout_marginRight="44dp"
     android:layout_marginTop="54dp"
     android:layout_centerInParent="true"
     android:src="@drawable/freelancer2" />
 
</RelativeLayout>
```
Once this layout is defined, we need to define the corresponding Activity class. Let’s name this class as AnimationActivity. The source code is as following:

```
package com.example.jellybeananimationexample;
 
import android.app.Activity;
import android.os.Bundle;
 
public class AnimationActivity extends Activity {
 
   @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_animation);
    }
}
```

Now, it’s time to define the MainActivity class having methods to customize the Activity animation.

```
package com.example.jellybeananimationexample;
 
import android.app.Activity;
import android.app.ActivityOptions;
import android.content.Intent;
import android.graphics.Bitmap;
import android.os.Bundle;
import android.view.View;
 
public class MainActivity extends Activity {
 
   @Override
   public void onCreate(Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity_main);
   }
 
   public void scaleupAnimation(View view) {
      // Create a scale-up animation that originates at the button
      // being pressed.
      ActivityOptions opts = ActivityOptions.makeScaleUpAnimation(view, 0, 0,
            view.getWidth(), view.getHeight());
      // Request the activity be started, using the custom animation options.
      startActivity(new Intent(MainActivity.this, AnimationActivity.class),
            opts.toBundle());
   }
 
   public void thumbNailScaleAnimation(View view) {
      view.setDrawingCacheEnabled(true);
      view.setPressed(false);
      view.refreshDrawableState();
      Bitmap bitmap = view.getDrawingCache();
      ActivityOptions opts = ActivityOptions.makeThumbnailScaleUpAnimation(
            view, bitmap, 0, 0);
      // Request the activity be started, using the custom animation options.
      startActivity(new Intent(MainActivity.this, AnimationActivity.class),
            opts.toBundle());
      view.setDrawingCacheEnabled(false);
   }
 
   public void fadeAnimation(View view) {
      ActivityOptions opts = ActivityOptions.makeCustomAnimation(
            MainActivity.this, R.anim.fade_in, R.anim.fade_out);
      // Request the activity be started, using the custom animation options.
      startActivity(new Intent(MainActivity.this, AnimationActivity.class),
            opts.toBundle());
   }
 
}
```
Once you are done with code, execute it. On clicking the application button, you will see the customized activity animation.
You can get the updated Android Animation source code from here.
For Android tutorial visit here.

---


原文地址：[http://code4reference.com/2012/08/customization-android-activity-animation/](http://code4reference.com/2012/08/customization-android-activity-animation/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)