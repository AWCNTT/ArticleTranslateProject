---
layout: post
title: "文章中文标题xxxx"
date: xxxx-xx-xx xx:xx
comments: true
categories: xxxxx(文章关键字，多个关键字用英文空格分割)
---

Mobile devices often have spotty network connectivity - rural areas, sitting in the subway or in a car are classic situations where the connection suffers. So mobile native or web apps need to take this into account and degrade gracefully when network requests time out or the response takes a long time to arrive. You can set network parameters in most emulators (see [here](http://developer.android.com/tools/devices/emulator.html#netdelay) for Android) or use a tool like the powerful [Charles proxy](http://www.charlesproxy.com/). But on a physical device this is of no use. This is especially useful to test different devices and platforms under real-life conditions.

Using a wireless router we can use Linux traffic shaping to add delay, slow down the throughput and also randomly drop packets. For this example I used OpenWRT because I’m familiar with it and because it has a long list of [supported routers](http://wiki.openwrt.org/toh/start)， but DD-WRT might work too. Get a supported router and a [snapshot](http://downloads.openwrt.org/snapshots/trunk/) image of the unstable Barrier Breaker release for the router. Flash it and set up your wifi. I ended up bridging the wifi to the WAN port since I did not need any routing:

![image](http://awcntt-article-image.qiniudn.com/issue103_tumblr_inline_n619bwxsBm1qcsghp.png)

Next, I created a shell script called qos.sh in /root:

``` shell
	#!/bin/sh

	# The bandwidth to simulate, here about 56kilobit per second. This is layer 2 bandwidth, so TCP/UDP and IP overhead will apply

	BW=”56kbps”

	# _Half_ the latency that we aim for. Since this applies to both the WAN port and Wi-Fi, the delay is applied twice, so this actually puts it at around 120+ms

	LATENCY=”60ms”

	# Chance of packet loss. Also applied to both interfaces, so it is 1%.

	LOSS=”0.5%”

	# The device name of your wifi device.

	WIFI=”wlan0”

	# The device name of your wan port

	WAN=”eth0.2”

	# Delete existing traffic control rules

	tc qdisc del root dev $WIFI

	tc qdisc del root dev $WAN

	# Create a basic tc rule for wifi

	tc qdisc add dev $WIFI root handle 1: htb default 12

	# First apply the rate limiting through a HTC scheduler

	tc class add dev $WIFI parent 1:1 classid 1:12 htb rate $BW ceil $BW

	# Then apply the netem scheduler which handles delay and packet loss

	tc qdisc add dev $WIFI parent 1:12 netem delay $LATENCY 10ms 25% loss $LOSS 

	# The same for WAN

	tc qdisc add dev $WAN root handle 2: htb default 12

	tc class add dev $WAN parent 2:1 classid 2:12 htb rate $BW ceil $BW

	tc qdisc add dev $WAN parent 2:12 netem delay $LATENCY 10ms 25% loss $LOSS 
```

You can download it from [this gist](https://gist.github.com/unverbraucht/118117ab66ac142f4eda). Mark it as executable with chmod a+x qos.sh and add the following to /etc/rc.local before the exit statement:

	/root/qos.sh

The delay and packet loss in one direction could probably be eliminated. Bandwidth limiting needs to be applied in both directions though. The script is based on this [StackExchange answer](http://superuser.com/a/147434). More info on netem can be found on this [OpenWRT wiki page](http://wiki.openwrt.org/doc/howto/packet.scheduler/sch_netem).

---


原文地址：[http://kevin-read.com/post/86601925386/simulating-a-slow-network-connection-when-testing-on](http://kevin-read.com/post/86601925386/simulating-a-slow-network-connection-when-testing-on)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)