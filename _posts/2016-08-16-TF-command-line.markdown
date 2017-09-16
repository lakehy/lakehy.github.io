---
layout: post
title:  "Team Foundation客户端命令行工具"
date:   2016-08-16 11:22:37 +0800
categories: guide
---
# Team Explorer Everywhere Command Line Client

## 下载

下载命令行客户端
[Microsoft/team-explorer-everywhere](https://github.com/Microsoft/team-explorer-everywhere)

## 安装

```bash
mkdir -p ~/.local/share/
cd ~/.local/share/
unzip TEE-CLC-Version.zip
mv TEE-CLC-Version TEE-CLC
cd
echo "export PATH=\$HOME/.local/share/TEE-CLC:\$PATH" > $HOME/.bashrc
```

## 使用帮助

```bash
tf help

export TFSC="http://mce-tfs03:8080/tfs/DefaultCollection"
export TFSD="\$/remote/dir/path"
export TFSL="the/local/path"
tf workspace -new xenial -collection:$TFSC
tf workfold -map -collection:$TFSC -workspace:xenial $TFSD $TFSL
tf workfolds
```

## 保存密码

```bash
export TF_AUTO_SAVE_CREDENTIALS=1
tf commands
```

## 参考

[1]. [Cross-Platform Command-Line Client- Beginner's Guide](https://msdn.microsoft.com/en-us/library/hh873092(v=vs.120).aspx)
