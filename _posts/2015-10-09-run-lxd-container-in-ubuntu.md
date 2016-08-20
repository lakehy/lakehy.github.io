---
layout: post
title: "LXD容器的安装与使用"
date:   2015-10-09 12:57:20 +0000
category: 教程
---
最初听到容器（container）这个名词是Docker引入的概念，大概类似于虚拟机对系统环境的隔离，又类似于Linux系统中的chroot环境的运行速度。
Docker是基于LXC技术开发的引擎，而Canonical公司基于LXC开发了LXD引擎，可以在Ubuntu系统中快速构建基于容器的隔离运行环境。

Ubuntu 16.04:  
```bash
sudo apt-get install lxd
```

查看可用的镜像源：  
```bash
lxc remote list
```

从官方网站下载云镜像：  
```bash
lxc launch ubuntu:xenial test1
```

使用如下命令即可登陆容器终端：  
```bash
lxc exec test1 bash
```
  
更多用法参考lxc命令的帮助。

从此可以随意折腾开发环境而不用担心时间和运行速度了。  
Just worked like a charm.:)

**引用**：  
[1]. <a href="https://linuxcontainers.org/lxd/getting-started-cli/" target="_blank">Installing LXD and the command line tool</a>
