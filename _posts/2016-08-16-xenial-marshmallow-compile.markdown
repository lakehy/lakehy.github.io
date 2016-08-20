---
layout: post
title:  Xenial安卓编译错误 
date:   2016-08-16 18:18:23 +0800
categories: guide
---

Building Android from sources: unsupported reloc 43

It works to me:
in file /art/build/Android.common_build.mk, find out:

{% highlight make %}
# Host.
ART_HOST_CLANG := false
ifneq ($(WITHOUT_HOST_CLANG),true)
  # By default, host builds use clang for better warnings.
  ART_HOST_CLANG := true
endif
{% endhighlight %}

change to :
{% highlight make %}
# Host.
ART_HOST_CLANG := false
ifeq ($(WITHOUT_HOST_CLANG),false)
  # By default, host builds use clang for better warnings.
  ART_HOST_CLANG := true
endif
{% endhighlight %}

If it still not works,try this in your android root path: 
cp /usr/bin/ld.gold prebuilts/gcc/linux-x86/host/x86_64-linux-glibc2.11-4.6/x86_64-linux/bin/ld

[See also](http://stackoverflow.com/questions/36048358/building-android-from-sources-unsupported-reloc-43)