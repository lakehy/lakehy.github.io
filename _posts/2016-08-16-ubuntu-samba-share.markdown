---
layout: post
title:  "Ubuntu设置Samba共享"
date:   2016-08-16 11:15:55 +0800
categories: guide
---
# samba设置

## 服务端

ubuntu 作为samba资源服务器，共享资源给其他机器

```bash
sudo apt-get install samba
sudo smbpasswd -a your-ubuntu-account
sudo adduser your-ubuntu-account sambashare
```

退出登录后重新登录unity桌面

## 客户端

ubuntu作为客户端，挂载其他机器的samba共享资源
