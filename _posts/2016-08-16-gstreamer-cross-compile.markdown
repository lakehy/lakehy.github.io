---
layout: post
title:  交叉编译Gstreamer
date:   2016-08-16 18:22:32 +0800
categories: guide
---

* get a Raspbian image and mount it below

``` 
 /usr/arm-linux-gnueabi
```

## gstreamer
{% highlight bash %}
./configure --host=arm-linux-gnueabihf --prefix=/usr/arm-linux-gnueabi/usr/local/ --disable-loadsave --disable-gtk-doc --disable-examples --includedir=/usr/arm-linux-gnueabi/usr/include/ --libdir=/usr/arm-linux-gnueabi/usr/local/lib/ --exec-prefix=/usr/arm-linux-gnueabi/usr/local/
make -j4
make install
{% endhighlight %}

## gst-plugins-base
{% highlight bash %}
export PKG_CONFIG_PATH="/home/RaspberryPi/gstreamer/pkgconfig/"
./configure --host=arm-linux-gnueabihf --prefix=/usr/arm-linux-gnueabi/usr/local/ --includedir=/usr/arm-linux-gnueabi/usr/include/ --libdir=/usr/arm-linux-gnueabi/usr/local/lib/ --exec-prefix=/usr/arm-linux-gnueabi/usr/local/ --bindir=/usr/arm-linux-gnueabi/usr/local/bin/ --disable-gtk-doc
make -j4
make install
{% endhighlight %}

## Links
* [QEMU raspberry pi the easy way](http://xecdesign.com/qemu-emulating-raspberry-pi-the-easy-way/)
* [Virtual Raspbian on Qemu in Ubuntu Linux 12.10](http://www.raspberrypi.org/phpBB3/viewtopic.php?f=29&t=37386)
* [Open Embedded way](http://stackoverflow.com/questions/13663292/non-tedious-way-to-cross-compile-third-party-libs-for-raspberry-pi)

