---
layout: post
title: "使用seafile将树莓派变为私有云盘"
date:   2015-10-14 12:57:20 +0000
category: 教程 
---
# seafile使用教程

自从出现大量的在线文档编辑与存储服务之后，个人文档的存储与共享变得方便起来了。
可是使用公有云存储始终有一些地方无法满足需求，
例如:

- 没有Linux客户端，
- 逐步取消了同步盘，
- 存储空间给的太小气，
- 担心数据安全。

要想满足这些“小众”的需求，就只有自己建服务器来实现了。

在网上搜索私有存储服务，可以找到ownCloud和seafile两款软件。
这两款都是开源软件，因此都可以免费使用。
同样因为开源，几乎所有平台都可以支持，如果官方没有支持，也可以自己编译。
seafile是国内的一个团队开发的，浏览了seafile的文档，部署的步骤比较简单，
并且seafile网站有预编译好的树莓派服务器端软件，于是选择seafile来部署。

## 安装seafile服务器

从seafile网站下载树莓派平台的服务器软件包到raspbian，安装依赖的软件包

    apt-get install python2.7 python-setuptools python-imaging sqlite3 python-flup nginx

解压并安装服务端

    mkdir -p haiwen/installed
    mv seafile-server_* haiwen/installed
    cd haiwei
    tar zxvf installed/seafile-server_*
    cd seafile-server-*
    ./setup-seafile.sh

安装脚本会问几个问题，默认设置即可。安装完成后运行服务

    ./seafile.sh start
    ./seahub.sh start

现在从浏览器打开地址 树莓派IP:8000，即可从局域网访问服务器。
登录安装时填写的账号试用看看。:)

## 如何从外网访问

上面的设置已经在局域网部署了seafile网盘服务了，可以满足个人日常使用要求了。
可是如果不在家，例如在公司时也想能够连接到家里的树莓派上的seafile服务器，就需要公网IP了。

现在电信运营商一般给家庭宽带分配的IP都是运营商多层路由器动态分配的大局域网IP，
并且家里也有可能使用多层路由，因此很有可能公网IP不可用。
这就需要DDNS将动态获取的IP地址映射到公网，使得在外部网络也可以访问这个映射出的公网地址。

花生壳内网版提供了这样的DDNS和端口映射服务。
即使没有公网IP，花生壳也可以将你的内网服务，映射到花生壳免费提供的域名+端口上。
在公司时，可以使用这个免费域名+端口访问家里的服务。

## 安装花生壳内网版

要使用花生壳内网版，需要在花生壳网站注册内网版，
[内网版注册地址](http://www.oray.com/activity/140902/?icn=oray_140902&ici=hsk_home-grid)，现在注册时需要花8元实名认证。

注册完成后，[下载花生壳内网版](http://hsk.oray.com/download/download?id=25)软件包。
按照[花生壳内网版安装文档](http://service.oray.com/question/2680.html)安装即可。
安装完成后，记下花生壳内网版设备编号，重启树莓派。

现在在花生壳网站注册免费域名，绑定设备刚才获得的树梅派上花生壳内网版设备编号。
绑定设备后，在[花生壳远程管理网站](http://b.oray.com/)添加端口映射，
需要的信息包括树莓派的内网IP和想要映射的内网端口。

花生壳会给你自动分配一个公网访问端口，形式类似于这样：

    name.oray.domain:portA ==> 192.168.1.100:portB

映射之后在浏览器访问 <http://name.oray.domain:portA> 即可访问家里的 192.168.1.100:portB 了。
为了防止家里的树梅派自动获取IP变化导致端口映射失效，可以将树梅派的地址设置为静态地址。

编辑树梅派上的/etc/network/interfaces, 修改如下:

```bash
#iface eth0 inet dhcp
iface eth0 inet static
    address 192.168.1.100
    netmask 255.255.255.0
    gateway 192.168.1.1
```

seafile服务使用了两个端口，8000用于提供文件浏览服务，8082用于文件传输。
免费的花生壳内网版最多可以映射两个内网端口，于是我弄成了这个样子，

~~~name.oray.domain:portA ==> 192.168.1.100:8000~~~

~~~name.oray.domain:portB ==> 192.168.1.100:8082~~~

这样访问name.oray.domain:portA，网页浏览可以登录账号，却不能下载/上传文件。
也就是只有文件浏览的8000端口可以正常使用，而8082端口访问异常。

查看seafile网站文档，[设置nginx反向代理](http://manual-cn.seafile.com/deploy/deploy_with_nginx.html)。
按照seafile网站的文档，设置如下，

花生壳映射：

    name.oray.domain:portA ==> 192.168.1.100：80 ==> (127.0.0.1:80)

Nginx代理:

```text
listen 127.0.0.1:80
127.0.0.1:8000 -> seafile webpage
127.0.0.1:8082 -> seafile sync
```

同样只能浏览网页，不能同步（下载/上传）文件，无论怎样修改nginx代理端口和本地seafile服务端口号。
最终发现，**花生壳分配的外网端口需要与树梅派局域网访问端口相同**。
在花生壳内网映射管理界面将局域网端口号修改为与花生壳自动分配的端口号相同，
nginx本地代理端口也改成相同的端口：

花生壳映射：

    name.oray.domain:portA ==> 192.168.1.100:portA (==> 127.0.0.1:portA)

Nginx代理:

```text
listen 127.0.0.1:80
127.0.0.1:8000 -> seafile webpage
127.0.0.1:8082 -> seafile sync
```

这样设置后，访问花生壳映射的地址+端口就可以正常使用了。
使用Nginx代理后，seafile的seahub服务启动方式变为fastcgi。重启seafile服务：

```bash
./seafile.sh restart
./seahub.sh stop
./seahub.sh start-fastcgi
```

现在测试从公共网络访问家里的服务器 name.oray.domain:portA ，可以登录账号并同步文档。
由于家庭宽带上行速率受限，通过公网访问速度可能不会很快，但是日常同步文档应该是可以使用的。

## 安全问题

使用以上设置后，从公共场所的WIFI网络访问家里的服务，可以正常使用。
考虑到在外网访问时账号的安全性问题，回家立即按照seafile文档将网站换成https连接。

[Nginx下启用Https](http://manual-cn.seafile.com/deploy/https_with_nginx.html)

## 从公司访问

在家试用seafile服务后，就迫不及待地想在公司使用了。
可是在公司网络却死活打不开网页，而使用移动网络却可以正常使用。

原来是公司封锁网络端口，只允许http/https端口(80/443)对外连接。
这下要想使用，只能把公网访问的端口portA设置为80或443。
使用花生壳的免费服务，只能获得随机分配的端口，不能够自己指定端口，除非换掉花生壳服务。

花生壳的端口映射服务，相当于免费提供了公网IP+端口，如果有自己可配置的公网IP，就可以指定使用80端口了。
之前有个搬瓦工VPS，用来搭梯子不太稳定，就闲置了，可以用来提供端口映射服务。

## 使用ngrok穿透内网

（待补充）
