---
layout: post
title:  "ubuntu16.04 不能拓展双屏的解决办法"
date:   2016-12-07
categories: ubuntu
comments: true
---

Ubuntu 16.04LTS extended display not working

The issue was with the nvidia graphics driver.This can be fixed by following the steps below :

{% highlight bash %}

1.Run sudo apt-get purge nvidia-*

2.Run sudo add-apt-repository ppa:graphics-drivers/ppa

3.Run sudo apt-get update

4.Run sudo apt-get install nvidia-364

{% endhighlight %}

Reboot and your graphics issue should be fixed.