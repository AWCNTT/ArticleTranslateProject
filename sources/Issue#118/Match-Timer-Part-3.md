---
layout: post
title: "Match Timer – Part 3"
date: 2014-09-12 10:34
comments: true
categories: BroadcastReceiver
---

Previously in this series we’ve looked at the software design decisions and the main timer engine for our Android Wear app to time do football (soccer to our American cousins) matches. In this article we’ll look at how we can wake up periodically in order to update the user.

![image](https://blog.stylingandroid.com/wp-content/uploads/2014/08/matchtimer.png)
 
matchtimerWe have already discussed how we don’t want to have a long running service in order to conserve battery, so we need to wake up periodically instead. The mechanism that we’ll use to do that is AlarmManager to periodically fire Intents which will trigger a BroadcastReceiver. I have chosen to use a BroadcastReceiver over an IntentService for the simple reason that the individual tasks we’ll be performing are extremely lightweight and short lived. Using a BroadcastReceiver will avoid the full Service lifecycle each time a task is performed, so is much more efficient for repeated small talks. The individual tasks that we need to perform will all execute in the order of magnitude of milliseconds.

At the heart of any BroadcastReceiver is the onReceive method, and this is where we shall marshall the various actions to their respective handlers:

MatchTimerReceiver.java

```
public class MatchTimerReceiver extends BroadcastReceiver {
    public static final String ACTION_START = "com.stylingandroid.matchtimer.ACTION_START";
    public static final String ACTION_STOP = "com.stylingandroid.matchtimer.ACTION_STOP";
    public static final String ACTION_PAUSE = "com.stylingandroid.matchtimer.ACTION_PAUSE";
    public static final String ACTION_RESUME = "com.stylingandroid.matchtimer.ACTION_RESUME";
    public static final String ACTION_RESET = "com.stylingandroid.matchtimer.ACTION_RESET";
    public static final String ACTION_UPDATE = "com.stylingandroid.matchtimer.ACTION_UPDATE";
    public static final String ACTION_ELAPSED_ALARM = "com.stylingandroid.matchtimer.ACTION_ELAPSED_ALARM";
    public static final String ACTION_FULL_TIME_ALARM = "com.stylingandroid.matchtimer.ACTION_FULL_TIME_ALARM";
    .
    .
    .
    @Override
    public void onReceive(Context context, Intent intent) {
        MatchTimer timer = MatchTimer.newInstance(context);
        boolean shouldUpdate = false;
        if (intent.getAction().equals(ACTION_UPDATE)) {
            shouldUpdate = true;
        } else if (intent.getAction().equals(ACTION_START)) {
            start(context, timer);
            shouldUpdate = true;
        } else if (intent.getAction().equals(ACTION_STOP)) {
            stop(context, timer);
            shouldUpdate = true;
        } else if (intent.getAction().equals(ACTION_PAUSE)) {
            pause(context, timer);
            shouldUpdate = true;
        } else if (intent.getAction().equals(ACTION_RESUME)) {
            resume(context, timer);
            shouldUpdate = true;
        } else if (intent.getAction().equals(ACTION_RESET)) {
            reset(timer);
        } else if (intent.getAction().equals(ACTION_ELAPSED_ALARM)) {
            elapsedAlarm(context);
        } else if (intent.getAction().equals(ACTION_FULL_TIME_ALARM)) {
            fullTimeAlarm(context);
        }
 
        if (shouldUpdate) {
            updateNotification(context, timer);
        }
    }
    .
    .
    .
}
```

This is pretty straightforward – we create a MatchTimer instance (which will be loaded from the values stored in SharedPreferences), and then pass that in to the individual action handler. After this is complete we then optionally, depending on the action which has been performed, update the Notification. More on this later.

We handle eight different actions: Five of them control the state of the timer (START, STOP, PAUSE, RESUME, RESET); one causes the notification to be updated; and two are for the vibration alarms which will be triggered when the total elapsed time and total played time reaches 45 minutes.

Let’s first with the state control actions:

MatchTimerReceiver.java

```
public class MatchTimerReceiver extends BroadcastReceiver {
    public static final int MINUTE_MILLIS = 60000;
    private static final long DURATION = 45 * MINUTE_MILLIS;
 
    private static final Intent UPDATE_INTENT = new Intent(ACTION_UPDATE);
    private static final Intent ELAPSED_ALARM = new Intent(ACTION_ELAPSED_ALARM);
    private static final Intent FULL_TIME_ALARM = new Intent(ACTION_FULL_TIME_ALARM);
 
    private static final int REQUEST_UPDATE = 1;
    private static final int REQUEST_ELAPSED = 2;
    private static final int REQUEST_FULL_TIME = 3;
 
    public static void setUpdate(Context context) {
        context.sendBroadcast(UPDATE_INTENT);
    }
    .
    .
    .
    private void reset(MatchTimer timer) {
        timer.reset();
    }
 
    private void resume(Context context, MatchTimer timer) {
        timer.resume();
        long playedEnd = timer.getStartTime() + timer.getTotalStoppages() + DURATION;
        if (playedEnd > System.currentTimeMillis()) {
            setAlarm(context, REQUEST_FULL_TIME, FULL_TIME_ALARM, playedEnd);
        }
    }
 
    private void pause(Context context, MatchTimer timer) {
        timer.pause();
        cancelAlarm(context, REQUEST_FULL_TIME, FULL_TIME_ALARM);
        long elapsedEnd = timer.getStartTime() + DURATION;
        if (!isAlarmSet(context, REQUEST_ELAPSED, ELAPSED_ALARM) && elapsedEnd > System.currentTimeMillis()) {
            setAlarm(context, REQUEST_ELAPSED, ELAPSED_ALARM, elapsedEnd);
        }
    }
 
    private void stop(Context context, MatchTimer timer) {
        timer.stop();
        cancelAlarm(context, REQUEST_UPDATE, UPDATE_INTENT);
        cancelAlarm(context, REQUEST_ELAPSED, ELAPSED_ALARM);
        cancelAlarm(context, REQUEST_FULL_TIME, FULL_TIME_ALARM);
    }
 
    private void start(Context context, MatchTimer timer) {
        timer.start();
        long elapsedEnd = timer.getStartTime() + DURATION;
        setRepeatingAlarm(context, REQUEST_UPDATE, UPDATE_INTENT);
        if (timer.getTotalStoppages() > 0 && !timer.isPaused()) {
            long playedEnd = timer.getStartTime() + timer.getTotalStoppages() + DURATION;
            if (playedEnd > System.currentTimeMillis()) {
                setAlarm(context, REQUEST_FULL_TIME, FULL_TIME_ALARM, playedEnd);
            }
            if (elapsedEnd > System.currentTimeMillis()) {
                setAlarm(context, REQUEST_ELAPSED, ELAPSED_ALARM, elapsedEnd);
            }
        } else {
            if (elapsedEnd > System.currentTimeMillis()) {
                setAlarm(context, REQUEST_FULL_TIME, FULL_TIME_ALARM, elapsedEnd);
            }
        }
    }
    .
    .
    .
}
```

These methods perform two functions. Firstly they set the state of the MatchTimer object; secondly they set the alarms for the elapsed and played alarms whenever the parameters change. There is also a utility method thrown in there for good measure named setUpdate() which will enable an external component to trigger an update.

The methods to set the alarms use the standard AlarmManager are:

MatchTimerReceiver.java

```
public class MatchTimerReceiver extends BroadcastReceiver {
    .
    .
    .
    public static final int MINUTE_MILLIS = 60000;
    .
    .
    .
 
    private void setRepeatingAlarm(Context context, int requestCode, Intent intent) {
        AlarmManager alarmManager = (AlarmManager) context.getSystemService(Context.ALARM_SERVICE);
        PendingIntent pendingIntent = PendingIntent.getBroadcast(context, requestCode, intent, PendingIntent.FLAG_UPDATE_CURRENT);
        alarmManager.setRepeating(AlarmManager.RTC_WAKEUP, System.currentTimeMillis(), MINUTE_MILLIS, pendingIntent);
    }
 
    private boolean isAlarmSet(Context context, int requestCode, Intent intent) {
        return PendingIntent.getBroadcast(context, requestCode, intent, PendingIntent.FLAG_NO_CREATE) != null;
    }
 
    private void setAlarm(Context context, int requestCode, Intent intent, long time) {
        AlarmManager alarmManager = (AlarmManager) context.getSystemService(Context.ALARM_SERVICE);
        PendingIntent pendingIntent = PendingIntent.getBroadcast(context, requestCode, intent, PendingIntent.FLAG_UPDATE_CURRENT);
        alarmManager.setExact(AlarmManager.RTC_WAKEUP, time, pendingIntent);
    }
 
    private void cancelAlarm(Context context, int requestCode, Intent intent) {
        PendingIntent pendingIntent = PendingIntent.getBroadcast(context, requestCode, intent, PendingIntent.FLAG_NO_CREATE);
        if (pendingIntent != null) {
            AlarmManager alarmManager = (AlarmManager) context.getSystemService(Context.ALARM_SERVICE);
            alarmManager.cancel(pendingIntent);
            pendingIntent.cancel();
        }
    }
    .
    .
    .
}
```
One thing here which is worth some discussion is the behaviour of setRepeatingAlarm() because it is a little different on Wear. This is used by the Start action to trigger an alarm every minute to update the notification, so there is always an accurate display of the number of minutes elapsed. The normally expected behaviour would be for this to trigger exactly 60 seconds after it was set, and then every 60 seconds from then onwards. However, it doesn’t quite work like this on Wear. What happens is: when the device is awake it will behave in this way, but as soon as the device goes to sleep this will be re-scheduled to trigger on exact minute boundaries. This is to synchronise the various components so that the device only needs to wake once per minute and that will tied to when the watch face app require an update at minute tick over.

For Match Timer it means that the elapsed minutes that are displayed may be one minute out, but when we’re displaying the elapsed minutes, it isn’t that time critical (it’s when we want second accuracy that we need to be completely accurate) so we can live with this.

The full time alarm handlers use the vibration service:

MatchTimerReceiver.java

```
public class MatchTimerReceiver extends BroadcastReceiver {
    .
    .
    .
    private static final long[] ELAPSED_PATTERN = {0, 500, 250, 500, 250, 500};
    private static final long[] FULL_TIME_PATTERN = {0, 1000, 500, 1000, 500, 1000};
 
    private void elapsedAlarm(Context context) {
        Vibrator vibrator = (Vibrator) context.getSystemService(Context.VIBRATOR_SERVICE);
        vibrator.vibrate(ELAPSED_PATTERN, -1);
    }
 
    private void fullTimeAlarm(Context context) {
        Vibrator vibrator = (Vibrator) context.getSystemService(Context.VIBRATOR_SERVICE);
        vibrator.vibrate(FULL_TIME_PATTERN, -1);
    }
    .
    .
    .
}
```

Finally we have the method to build the notification that gets displayed to the user:


```
public class MatchTimerReceiver extends BroadcastReceiver {
    public static final int NOTIFICATION_ID = 1;
    .
    .
    .
    private void updateNotification(Context context, MatchTimer timer) {
        NotificationBuilder builder = new NotificationBuilder(context, timer);
        Notification notification = builder.buildNotification();
        NotificationManagerCompat notificationManager = NotificationManagerCompat.from(context);
        notificationManager.notify(NOTIFICATION_ID, notification);
    }
}
```

Notifications are an important part of Wear and warrant a custom class to construct them for our purposes. In the next article we’ll cover the Notifications used by Match Timer.

Match Timer is available on [Google Play](https://play.google.com/store/apps/details?id=com.stylingandroid.matchtimer).


© 2014, [Mark Allison](https://blog.stylingandroid.com/). All rights reserved. This article originally appeared on [Styling Android](http://blog.stylingandroid.com/).

---


原文地址：[https://blog.stylingandroid.com/match-timer-part-3/](https://blog.stylingandroid.com/match-timer-part-3/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)