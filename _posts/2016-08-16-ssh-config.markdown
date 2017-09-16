---
layout: post
title:  "Gerrit服务器SSH克隆错误"
date:   2016-08-16 14:15:35 +0800
categories: guide
---
# 指定SSH秘钥算法

使用ssh协议从gerrit服务器克隆代码时，会出现错误：

```bash
$ Unable to negotiate with gerrit-server port 29418: no matching key exchange method found. Their offer: diffie-hellman-group1-sha1
fatal: Could not read from remote repository.
```

解决办法是指定ssh的秘钥算法。新建或编辑文件 *~/.ssh/config* ，添加如下内容

```text
Host gerritserver
    KexAlgorithms +diffie-hellman-group1-sha1
```
