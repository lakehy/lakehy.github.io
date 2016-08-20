---
layout: post
title: "bitbake从本地仓库获取源文件"
date:   2015-10-05 12:57:20 +0000
category: 教程 
---
[bitbake fetcher](http://www.yoctoproject.org/docs/1.8/bitbake-user-manual/bitbake-user-manual.html#bb-fetchers)支持多种协议来获取文件，
这些协议大多是从网络服务器获取文件。有时新的修改可能还没有提交到服务器，从本机获取文件可快速测试最新功能。

本地的代码可以是无版本信息的文件(夹)，也可以是带版本控制信息的本地仓库。
这里简要介绍如何从本地git仓库更新代码，其他协议参考git的设置即可。

### 本地Git克隆仓库

bitbake中的 [Git Fetcher](http://www.yoctoproject.org/docs/1.8/bitbake-user-manual/bitbake-user-manual.html#git-fetcher)
使用以下形式的变量指定git仓库的下载参数:

```bash
SRC_URI = "git://<host>/path/to/<repo>.git;protocol=<protocol>;branch=<branch>;user=<user>:<password>;destsuffix=<destdir>"
SRCREV = ${AUTOREV}
```

注意SRC_URI中的路径必须以“git://”开头，bitbake fetcher才会使用Git Fetcher来更新。  
protocol： 指定了git fetcher使用的协议(git/file等等)。此处使用**file://**协议来克隆本地磁盘上的仓库。  
branch： 分支名  
user： 用户名/密码  
destsuffix：目的文件夹名  

SRCREV指定了获取源代码的版本号，“AUTOREV”表示获取当前分支最新的版本。



### 本地文件

本地git仓库中的代码，也可以打包成压缩包：  

```bash
$ git archive --prefix=src_dir_name/ HEAD -o ../src_dir_name.tar.bz2
```

这样这个压缩包中就不包含版本控制信息，指定bitbake使用**file://**协议获取：

```bash
SRC_URI = "file:///local/source/path"
```

### 更新文件

修改完源代码的获取路径之后，使用bitbake命令重新编译/打包代码：

```bash
$ bitbake gst-fsl-plugin -c clean
$ bitbake gst-fsl-plugin -c compile -f
$ bitbake gst-fsl-plugin
```
