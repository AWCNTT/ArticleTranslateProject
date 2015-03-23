---
layout: post
title: "在移动设备上进行测试时模拟一个慢速网络连接"
date: 2015-03-22 23:30
comments: true
categories: 移动设备 模拟慢速网络 测试
---

移动设备的网络连接通常不太稳定－比如在农村，坐在地铁上或车里都会发生连接问题。所以移动原生应用或移动web应用必须考虑慢速网络，同时尽量减轻网络请求超时或长时间响应给用户带来的不满。为了模拟慢速连接，在大多数模拟器上你可以通过设置网络参数(对于安卓设备可以参考[这里](http://developer.android.com/tools/devices/emulator.html#netdelay))，或者使用一个工具－[Charles proxy](http://www.charlesproxy.com/)。
但这在实际物理设备上不管用。这主要用于真实环境下的不同设备和平台。

通过无线路由器，我们可以使用linux流量整形（Linux traffic shaping）来增加延时，减缓通信吞吐速度并随机丢弃数据包。在这个例子中我使用了OpenWRT因为我比较熟悉，而且[OpenWRT支持很多路由器](http://wiki.openwrt.org/toh/start)，不过DD-WRT也同样有用。首先要有一个支持的路由器和一个[不稳定Barrier Breaker镜像](http://downloads.openwrt.org/snapshots/trunk/)。flash 它然后安装你的wifi。最后因为我并不需要路由，所以我将wifi连接到WAN口。
[参考图片](http://awcntt-article-image.qiniudn.com/issue103_tumblr_inline_n619bwxsBm1qcsghp.png)

然后，我在 /root 目录下创建一个脚本 qos.sh：

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
你可以从[这里](https://gist.github.com/unverbraucht/118117ab66ac142f4eda)下载脚本文件。
使用chmod a+x qos.sh命令将它转换为可执行文件并且在退出前，将下面的代码加入/etc/rc.local中：

	/root/qos.sh

单向的延时和丢包可能会被终止，我们应该在双向都进行带宽限制。这里的脚本是基于[StackExchange 回答](http://superuser.com/a/147434)。你可以在[OpenWRT 维基百科页面](http://wiki.openwrt.org/doc/howto/packet.scheduler/sch_netem)查看更多关于netem信息。

---


原文地址：[http://kevin-read.com/post/86601925386/simulating-a-slow-network-connection-when-testing-on](http://kevin-read.com/post/86601925386/simulating-a-slow-network-connection-when-testing-on)

译者：[wingjay](https://github.com/wingjay) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)