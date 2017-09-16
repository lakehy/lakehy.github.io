---
layout: post
title:  Xenial安卓6编译环境安装
date:   2016-08-16 15:25:35 +0800
categories: guide
---
# 设置步骤

## 首先删除之前安装的不匹配的Java版本

    sudo apt-get remove openjdk-8-*

## 添加软件源

```bash
sudo add-apt-repository ppa:openjdk-r/ppa
sudo add-apt-repository ppa:git-core/ppa
sudo add-apt-repository ppa:saiarcot895/myppa
sudo apt-get update
sudo apt-get install apt-fast

wget https://raw.githubusercontent.com/ilikenwf/apt-fast/master/completions/bash/apt-fast
sudo mv apt-fast /etc/bash_completion.d/
```

## 安装其他依赖的软件包

```bash
sudo apt-fast install ccache automake lzop bison flex gperf build-essential zip curl zlib1g-dev \
g++-multilib libxml2-utils bzip2 libbz2-dev libbz2-1.0 squashfs-tools pngcrush \
schedtool dpkg-dev liblz4-tool make bc u-boot-tools \
openjdk-7-jdk git
```

## 添加环境变量

设置 **repo** 工具 和 **ccache**

```bash
mkdir ~/.local/bin
curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/.local/bin/repo
chmod a+x ~/.local/bin/repo

echo "export PATH=\$HOME/.local/bin:\$PATH" >> ~/.profile
echo "export USE_CCACHE=1" >> ~/.profile
```

## 下载及编译

参考VAB-820 Android 6 [Quick Guide](/devtips/imx6/android/vab820-android6-dev-guide.html)

## 参考

[1]. [Setup Ubuntu 16.04 LTS Xenial Xerus to Compile Android ROMs](https://nathanpfry.com/how-to-setup-ubuntu-16-04-lts-xenial-xerus-to-compile-android-roms/)
