---
layout: post
title:  "Team Foundation客户端命令行工具"
date:   2016-08-16 11:22:37 +0800
categories: guide
---
## 下载
下载命令行客户端  
[TEE-CLC-12.0.2.zip](https://www.microsoft.com/en-us/download/details.aspx?id=40785)  

## 安装
参考[Cross-Platform Command-Line Client- Beginner's Guide](https://msdn.microsoft.com/en-us/library/hh873092(v=vs.120).aspx)
{% highlight bash %}
mkdir -p tfsspace/local/dir
cd tfsspace
tf workspace -new xenial -collection:http://tfserv/tfs/DefaultCollection
tf workfold -map -collection:http://tfserv/tfs/DefaultCollection \
-workspace:xenial $/remote/dir local/dir
{% endhighlight %}

## 使用帮助
[Command-line tools for TFS](https://msdn.microsoft.com/en-us/library/ms253088(v=vs.120).aspx)

## 参考
[1]. [Cross-Platform Command-Line Client- Beginner's Guide](https://msdn.microsoft.com/en-us/library/hh873092(v=vs.120).aspx)
